# Pronoun-Transformation Activity — Design & Implementation Plan

**Status:** Design document for review. **No implementation code written yet.**
**Predecessor:** `PRONOUN_TRANSFORM_FEASIBILITY.md` (same branch,
`claude/pronoun-transform-feasibility-xde2xo`), which recommended **Path B**
(new activity type on the Sentence-Builder skeleton, DepannagePanel reused for
feedback only) with **deterministic canonical + normalized-variant grading** to
start, and the `proofread-writing` LLM pattern held in reserve.
**Date:** 2026-07-16

This plan follows the rigor of the Content Batch Protocol
(`concordance-platform/CONTRIBUTING.md` §⑧) and mirrors the concrete patterns of
the existing Sentence Builder pipeline (item table + `GET items` / `POST review`
route pair + base-sentence-array + generation script + admin publish queue).

---

## 0. The activity in one paragraph

Student is shown a full French sentence — *« Je donne le livre à Marie. »* — and
must **type the pronoun-transformed sentence** — *« Je le lui donne. »*. On
submit, the server grades the typed sentence deterministically against a
canonical answer (plus authored variants), records the result to SM-2/BKT exactly
as Sentence Builder does, and returns feedback rendered in `DepannagePanel`: the
correct sentence with the pronouns bolded, plus (optionally) the source sentence
with the replaced noun phrases struck through to show *what disappeared*.

Input is free text (meets the product requirement); grading stays deterministic
(no LLM in the correctness path); the pronoun-transform answer space is small and
rule-governed, which is what makes deterministic grading tractable.

---

## 1. Data model

### 1.1 New table vs. extending `sentence_builder_items`

**Recommendation: a new sibling table, `pronoun_transform_items`.** Not an
extension of `sentence_builder_items`.

Rationale — `sentence_builder_items` is *tile-centric*: its entire grading
contract is "assembled non-distractor **tile IDs** equal the stored order"
(`student.js` `POST /sentence-builder/:id/review`), and its `tiles jsonb` +
`sentence_builder_tile_concepts` join exist only to serve that contract. A
free-text transform has no tiles, no distractors, and no tile-concept grain.
Overloading `sentence_builder_items` would mean a permanently-`NULL` `tiles`
column, an unused join table, and a second grading contract hidden inside one
table — the exact "sibling, not reuse" reasoning the `migration_sentence_builder.sql`
header already applies to `sentence_builder_tile_concepts` vs `exercise_concepts`.
A dedicated table keeps each activity's grading contract clean and auditable.

The new table reuses every established column convention (`level`, `direction`,
`register`, `source_batch`, `is_published`, `explanation_ref` FK →
`grammar_concepts`, bounded JSONB read only with its parent row).

### 1.2 Proposed schema

```sql
-- db_migrations/migration_pronoun_transform.sql
-- Pronoun-Transformation (Transformation pronominale) — a free-text produce-the-
-- sentence activity. The student retypes a full sentence with its object noun
-- phrases replaced by the correct object pronouns in the correct order. Grading
-- is deterministic: normalized string comparison against canonical_answer and
-- accepted_variants (see §2). Feeds the existing `pronoun_placement` BKT skill.
--
-- Data-model notes (mirrors sentence_builder_items conventions):
--   • canonical_answer / accepted_variants / pronouns / *_annotation are bounded
--     JSONB or text, always read with their parent row, never queried across.
--   • No tiles, no distractor join table — grading is string-based, per-item, so
--     concept tagging is per-item (explanation_ref), the exercise_concepts grain,
--     NOT the per-tile grain sentence_builder_tile_concepts uses.
--   • is_published defaults false so the generation pipeline lands rows in the
--     admin review queue (same flow the SB generators use), published on review.

create table if not exists pronoun_transform_items (
  id                 serial primary key,
  level              text not null,                     -- ACTFL band ('N1'..'Adv'), lib/bands.js BAND_ORDER
  direction          text not null default 'fr_to_fr'
                       check (direction in ('fr_to_fr')),-- always FR→FR for a transform
  register           text,                              -- nullable, mirrors migration_sentence_builder_register
  source_sentence    text not null,                     -- "Je donne le livre à Marie."
  canonical_answer   text not null,                     -- "Je le lui donne."
  accepted_variants  jsonb not null default '[]'::jsonb,-- ["Je le donne à elle."?] additional FULL-sentence forms that are also correct (see §2.4)
  pronouns           jsonb not null default '[]'::jsonb,-- [{ "form":"le","role":"cod","replaces":"le livre" }, { "form":"lui","role":"coi","replaces":"à Marie" }]
  corrected_sentence jsonb,                             -- DepannagePanel span shape for the ANSWER: [{t:"Je "},{t:"le",bold:"pronom"},{t:" "},{t:"lui",bold:"pronom"},{t:" donne."}]
  source_annotation  jsonb,                             -- OPTIONAL: SOURCE with strike spans for "what disappeared": [{t:"Je donne "},{t:"le livre",strike:true},{t:" "},{t:"à Marie",strike:true},{t:"."}]
  explanation_ref    integer references grammar_concepts(id) on delete set null,
  source_batch       text,
  is_published       boolean default false,
  created_at         timestamptz default now()
);

create index if not exists idx_pti_level_dir
  on pronoun_transform_items (level, direction)
  where is_published;

create index if not exists idx_pti_source_batch
  on pronoun_transform_items (source_batch);
```

Column intent:

| Column | Purpose |
|---|---|
| `source_sentence` | The prompt the student transforms. |
| `canonical_answer` | The single reference correct sentence; the normalized-comparison target (§2). |
| `accepted_variants` | Authored extra full-sentence forms that are *also* correct when an item genuinely has >1 acceptable surface form. Most orthographic flexibility is handled by normalization, not here (§2.4). |
| `pronouns` | Per-pronoun metadata (`form`, `role` ∈ cod/coi/reflexive/y/en, `replaces`). Drives feedback ("right pronouns, wrong order") and the annotation displays; also documents authorial intent for review. |
| `corrected_sentence` | Reuses the **existing** DepannagePanel bold-span shape so the feedback panel needs no new answer-rendering code. |
| `source_annotation` | Optional authored strike-through markup of the source, powering the "what moved/disappeared" visualization. Needs a small DepannagePanel extension to render `strike` spans (§4.3) — flagged as net-new. |
| `is_published` | `false` at insert → admin review queue → published (same as SB generators). |

No new concept table, no tile-concept join.

---

## 2. Grading logic

Deterministic, server-side, in `POST /student/pronoun-transform/:itemId/review`.
The student's typed sentence is normalized and compared to the normalized
`canonical_answer` and each normalized `accepted_variants` entry. **Only an exact
normalized match counts as correct for BKT/SM-2** — the near-miss tiers below
change *feedback only*, never credit, matching the exact-match discipline every
other discrete item already enforces.

### 2.1 Why deterministic is tractable here

French clitic object-pronoun placement is rule-governed with a **fixed** order,
so for a given source there is essentially one correct surface string (modulo
orthography). Preverbal order (before the conjugated verb, or before the
infinitive it complements):

```
1. me / te / se / nous / vous
2. le / la / les
3. lui / leur
4. y
5. en
```

e.g. *me le* (Il me le donne), *le lui* (Il le lui donne), *y en* (Il y en a).
Affirmative imperative moves the clitics *after* the verb, hyphenated, with
direct-before-indirect order and `me/te → moi/toi` (Donne-**le-moi**); negative
imperative reverts to preverbal (Ne me le donne pas). Because the target is
determined by rule, the "flexibility" is almost entirely **orthographic** — which
is exactly what a well-specified normalizer collapses.

### 2.2 The normalization pipeline (`normalizePronounAnswer`)

Applied identically to the student input, `canonical_answer`, and each
`accepted_variants` entry. Ordered steps, each justified by real French pronoun
orthography:

1. **Unicode NFC.** Compose so accented characters compare byte-stably.
2. **Trim + collapse whitespace.** `\s+` → single space; strip leading/trailing.
3. **Lowercase.** Neutralizes the sentence-initial capital (*Je* vs *je*). Proper
   nouns mostly vanish in the transform (Marie → lui), so this is low-risk.
   Accents are **preserved** at this stage.
4. **Apostrophe unification + elision tightening.** Map every apostrophe variant
   (`U+2019 ’`, `U+02BC ʼ`, backtick) to straight `U+0027 '`; then remove any
   whitespace around it: `l ' aime` / `l' aime` → `l'aime`. Elision joins tightly
   in French (*l'aime*, *m'en*, *j'ai*, *n'aime*, *qu'il*), so a spaced apostrophe
   is a typing artifact, not a different answer.
5. **Dash unification.** Map `–` `—` `‑` to ASCII `-`.
6. **Terminal-punctuation strip.** Remove trailing `.`, `!`, `?`, `…` and wrapping
   quotation marks. Sentence *type* is preserved by the exercise, so a missing
   final period is not an error. Internal commas are **kept** (a dislocation like
   *Le livre, je le lui donne* is a different structure → an `accepted_variants`
   entry, not a normalization).

The result of steps 1–6 is the **core form** used for the correctness decision,
with **one deliberate branch** for hyphenation (step 7):

7. **Hyphen handling — two derived forms.**
   - `core` = steps 1–6 **with hyphens converted to spaces**
     (`donne-le-moi` → `donne le moi`). Correctness does **not** require the
     student to hyphenate the imperative — a real pedagogical convenience, since
     hyphenation is a separate sub-skill.
   - `hyphenExact` = steps 1–6 **preserving hyphens**. Used only to decide whether
     to *append a hyphenation nudge* to otherwise-correct feedback
     ("n'oubliez pas les traits d'union : donne-le-moi").

   So an imperative answer is graded correct with or without hyphens, but the
   student is still told when hyphens are missing. Deterministic, no partial
   credit into BKT.

### 2.3 Grading tiers (feedback resolution)

Let `N()` be the `core` normalizer, `A` = accepted set = `{canonical_answer} ∪
accepted_variants`, and `P()` extract the clitic multiset from the known
inventory `{me, m', te, t', se, s', le, la, l', les, lui, leur, nous, vous, y, en,
moi, toi}`.

| Tier | Condition | `correct` (BKT/SM-2) | Feedback |
|---|---|---|---|
| **1 — Correct** | `N(input) ∈ N(A)` | **true** | Bravo; if `hyphenExact` differs from a hyphenated canonical, append the hyphenation nudge. |
| **2 — Accent/spelling near-miss** | `stripAccents(N(input)) ∈ stripAccents(N(A))` | false | "Bon placement — vérifiez les accents / l'orthographe." |
| **3 — Right pronouns, wrong order** | `P(input)` multiset `== P(canonical)` but Tier 1/2 fail | false | "Les bons pronoms, mais l'ordre n'est pas correct (ordre : me/te/se · le/la/les · lui/leur · y · en)." |
| **4 — Wrong pronouns / other** | else | false | Generic incorrect + reveal canonical. |

Tiers 2–4 are strictly feedback; they never feed credit. This is the "richer
feedback without a fuzzy grader" the feasibility doc called for. If Tier-3
mis-order or Tier-4 cases prove too coarse in review, the `proofread-writing`
LLM pattern is the pre-approved fallback for equivalence-judging — but is **out of
scope for v1**.

### 2.4 What goes in `accepted_variants` (and what does not)

- **Handled by normalization (NOT variants):** capitalization, trailing period,
  apostrophe style, spaced apostrophe, imperative hyphen-vs-space, dash style.
- **Genuine variants (authored, rare):** cases with >1 correct *structure*, e.g.
  an item that legitimately allows a dislocated form, or a modal+infinitive where
  the corpus intends to accept a second placement. These are enumerated by the
  author at content time and validated by the generation script (§5), not guessed
  at runtime. Expectation: the large majority of items ship with
  `accepted_variants: []`.

### 2.5 Anti-gaming parity

`revealed` (opening help before submitting) forces a failing SM-2 quality and
`correct=false` into BKT, identical to `student.js` `POST /sentence-builder/:id/review`
(`quality = revealed ? QUALITY.difficile : ...`). Single server-side choke point,
same as every other activity.

---

## 3. API surface

Two routes, modeled 1:1 on the Sentence Builder pair in `student.js`.

### 3.1 `GET /student/pronoun-transform/items?level=&limit=`

Returns a batch of items **with the answers stripped** (never leak
`canonical_answer` / `accepted_variants` to the client — same discipline as SB
shuffling tiles server-side).

Response (200):
```json
[
  {
    "id": 812,
    "level": "B1",
    "sourceSentence": "Je donne le livre à Marie.",
    "conceptExplanation": "Les pronoms compléments (me, te, le, la, l', ...) se placent ..."
  }
]
```
`conceptExplanation` is the joined `grammar_concepts.explanation` (via
`explanation_ref`), needed for the pre-submit reveal panel. No pronoun count, no
answer, no annotations are sent pre-submit.

### 3.2 `POST /student/pronoun-transform/:itemId/review`

Request:
```json
{ "answer": "Je le lui donne", "revealed": false }
```

Server: load item → `normalizePronounAnswer` → tier resolution (§2.3) →
`activity_progress` SM-2 upsert (`reviewItem`, `activity_type = 'pronoun-transform'`)
→ `recordSkillObservation(userId, 'pronoun_placement', correct && !revealed,
'pronoun-transform')` → `updateStudentBKT(userId)`. Identical plumbing to the SB
review route; the only differences are the correctness computation (string tiers
vs tile-ID equality) and no tile-concept loop.

Response (200):
```json
{
  "correct": true,
  "tier": 1,
  "feedbackCode": "correct",            // "correct" | "accent_near_miss" | "wrong_order" | "incorrect"
  "hyphenationNudge": false,
  "canonicalAnswer": "Je le lui donne.",// safe to reveal post-submit
  "correctedSentence": [ { "t": "Je " }, { "t": "le", "bold": "pronom" }, { "t": " " }, { "t": "lui", "bold": "pronom" }, { "t": " donne." } ],
  "sourceAnnotation": [ { "t": "Je donne " }, { "t": "le livre", "strike": true }, { "t": " " }, { "t": "à Marie", "strike": true }, { "t": "." } ],
  "conceptExplanation": "…",
  "nextDueAt": "2026-07-20T00:00:00.000Z"
}
```
`feedbackCode` (not a raw French string) lets the frontend localize via i18n
(§⑥/§① require student-facing strings to be translatable).

---

## 4. Frontend

### 4.1 New page component (`src/pages/PronounTransform.jsx`)

Modeled on `SentenceBuilder.jsx`, with the tile bank replaced by a single text
input. Wireframe:

```
┌─ TRANSFORMATION PRONOMINALE ──────────────────────────────┐
│  Progress: 3 / 20                        [ScoreBar]        │
│                                                            │
│  Réécrivez la phrase en remplaçant les groupes soulignés   │
│  par les pronoms qui conviennent :                         │
│                                                            │
│     « Je donne le livre à Marie. »                         │
│                                                            │
│  ┌──────────────────────────────────────────────┐         │
│  │ Je le lui donne_                               │ ← <input>/<textarea>, autofocus
│  └──────────────────────────────────────────────┘         │
│                                                            │
│  [ Vérifier ]   [ 🔧 Afficher l'aide ]  ← reveal ⇒ revealed=true (forfeits credit)
│                                                            │
│  ── after submit ──────────────────────────────────────    │
│  ✓ Correct ! Bravo.        (or tiered feedback message)    │
│  ┌── DepannagePanel ────────────────────────────────┐      │
│  │ PHRASE CORRIGÉE:  Je **le** **lui** donne.        │ ← corrected_sentence (bold pronouns)
│  │ (optional) SOURCE:  Je donne ~~le livre~~ ~~à Marie~~. │ ← source_annotation (strike)
│  │ EXPLICATION:  concept text (FR/EN toggle)         │      │
│  └───────────────────────────────────────────────────┘     │
│  [ Continuer ]                                             │
└────────────────────────────────────────────────────────────┘
```

State/flow mirrors `SentenceBuilder.jsx`: `items` (fetched once), `index`,
`answer` (the typed string), `revealed`, `result`, `submitting`, `panelLang`;
`useScore('pronoun-transform')` for the local session score; submit posts to
`/pronoun-transform/:id/review`; `res.correct ? addCorrect() : addWrong()`.

### 4.2 Feedback reuse

`DepannagePanel` is reused **unchanged** for `correctedSentence` (bold pronouns) +
`specificFeedback` (the tiered message, resolved from `feedbackCode` via i18n) +
`explanation` (concept). This is the "reuse the feedback surface" the feasibility
doc endorsed — zero changes to DepannagePanel for the answer display.

### 4.3 One scoped DepannagePanel extension (only if we ship `source_annotation`)

To render the "what disappeared" strike-through, `CorrectedSentence` in
`DepannagePanel.jsx` currently handles only `bold`:
```js
seg.bold ? <strong>{seg.t}</strong> : <span>{seg.t}</span>
```
It needs a `strike` branch (`<s>` / `line-through`) and a second labeled row for
the source annotation. Small, additive, backward-compatible (existing callers pass
no `strike`). **Flagged as net-new display work**, per the feasibility doc's note
that movement visualization is free on neither path. If we defer the annotation,
DepannagePanel needs no change at all and v1 ships the bold corrected-answer only.

### 4.4 Registration surface (the known handful)

- `src/App.jsx` — one `React.lazy` import + `<Route path='/student/practice/pronoun-transform' element={<PronounTransform/>} />`.
- `src/pages/student/Practice.jsx` — one entry in the `category_grammar` items
  array: `{ to: '/student/practice/pronoun-transform', slug: 'pronoun-transform', labelKey: 'student.practice.activity_pronoun_transform' }`.
- `src/locales/{fr,en}/translation.json` — `activity_pronoun_transform`, the
  page/instruction strings, and the four `feedbackCode` messages (§① i18n is
  required for all student-facing strings).

---

## 5. Generation / authoring pipeline

Follows the established **base-sentence-array + generation-script + admin-publish**
pattern, but with a crucial difference in the division of labor.

### 5.1 What must be authored vs. what a script derives

In Sentence Builder the model produces the *trap distractor* (the one hard part).
Here, **the canonical answer and the pronoun forms/order ARE the pedagogy** — they
must be human-authored and reviewed, never model-invented, because a wrong
canonical silently mis-grades every attempt. So the split is:

- **Hand-authored** (in the base-sentence array): `source_sentence`,
  `canonical_answer`, `pronouns[]` (form/role/replaces), `level`, `conceptName`,
  and any genuine `accepted_variants`. This is small, bounded, rule-checkable
  content — the same shape as `accordPronomsComplementsBaseSentences.js`.
- **Deterministically derived by the script** (pure, unit-testable, no model):
  1. `corrected_sentence` spans — locate each `pronouns[].form` in
     `canonical_answer` and wrap it as `{t, bold:'pronom'}`, fixed text between as
     plain `{t}` (the inverse of `deriveTiles`; reuses the same span discipline).
  2. `source_annotation` spans — locate each `pronouns[].replaces` in
     `source_sentence` and wrap it as `{t, strike:true}`.
  3. **Validation** (reject before it reaches review):
     - `canonical_answer` must contain each declared pronoun `form` as a token;
     - the declared clitic order must satisfy the fixed order table (§2.1) — a
       deterministic order-lint that catches an author typo like *lui le*;
     - `source_sentence` must contain each `replaces` substring;
     - `canonical_answer` must **not** contain any `replaces` substring (the noun
       phrase was actually replaced);
     - each `accepted_variants` entry must normalize (§2.2) to something whose
       clitic multiset equals the canonical's (a variant may reorder structure but
       not change which pronouns are used) — else flag for author review.

The model's role shrinks to **zero for v1** (everything is authored or
deterministically derived). The `proofread-writing`-style LLM call remains the
reserved fallback for *suggesting* additional `accepted_variants` at authoring
time only — not in the grading path.

### 5.2 Files

- `src/data/pronounTransformBaseSentences.js` — the hand-authored corpus:
  ```js
  export const pronounTransformBaseSentences = [
    {
      sourceId: 6001,
      source_sentence: "Je donne le livre à Marie.",
      canonical_answer: "Je le lui donne.",
      accepted_variants: [],
      pronouns: [
        { form: "le",  role: "cod", replaces: "le livre" },
        { form: "lui", role: "coi", replaces: "à Marie" },
      ],
      conceptName: "pronoun_placement",
      level: "B1",
    },
    // ...
  ]
  ```
- `scripts/generate-pronoun-transform.js` — mirrors
  `generate-sentence-builder-accord-pronoms-complements.js`: `--sample` /
  `--dry-run` / `--limit` / `--offset` / `--source-batch`;
  `startGenerationRun` / `finishGenerationRun` / `failGenerationRun`; skips rows
  already present by `source_sentence` in the batch; inserts `is_published=false`.
  For each base: run the deterministic derivations + validators (§5.1), then
  `insert into pronoun_transform_items (...)` with
  `explanation_ref = (select id from grammar_concepts where name = conceptName)`.
- `src/lib/pronounTransform.js` — the pure derivation/validation/normalization
  functions (`normalizePronounAnswer`, `deriveCorrectedSpans`,
  `deriveSourceAnnotation`, `validateItem`, `gradeAnswer`), unit-tested the way
  `sentenceBuilderGeneration.test.js` tests its pure pieces. Sharing
  `gradeAnswer`/`normalizePronounAnswer` between the review route and the tests
  guarantees the grader that runs in prod is the grader under test.
- `db_migrations/migration_pronoun_transform.sql` (§1.2), **added to
  `MIGRATION_ORDER` in `scripts/migrate.js`** (the array is explicit, not
  alphabetical, and `migrate.js` fails loudly if a `migration_*.sql` file is
  unlisted). No new concept migration — `pronoun_placement` is already seeded and
  already in `MIGRATION_ORDER`
  (`migration_sentence_builder_pronoun_placement_concept.sql`).

### 5.3 Batch tagging

`source_batch = 'pronounTransform_2026'` set at every insert path (§⑧ Gate 3).

---

## 6. Concept tagging

**Maps to the existing `pronoun_placement` concept — no new concept needed.**

- The row already exists and is already migrated
  (`migration_sentence_builder_pronoun_placement_concept.sql`, seeded at ACTFL
  band A2 / skill band I2 in `skillBands.js`).
- It is a real BKT skill key: `ACTIVITY_SKILL_MAP` already maps
  `'pronoms-complements' → 'pronoun_placement'`, and the AI evaluators' error
  tags resolve toward it (`skillTracing.js`). Feeding this activity into
  `pronoun_placement` **unifies** typed-transform practice with the existing
  deterministic pronoun practice and the AI error stream — the same deliberate
  unification the concept was designed for.

**One required wiring change (flag):** add a new **activity** key to
`ACTIVITY_SKILL_MAP` in `concordance-api/src/lib/skillTracing.js`:
`'pronoun-transform': 'pronoun_placement'`. The *concept* is reused; the
*activity slug* is new (so `recordSkillObservation(..., 'pronoun-transform')`
attributes observations correctly while still moving the shared skill belief).

**Level note (for product):** `pronoun_placement` is banded A2/I2, but
multi-pronoun reordering (*le lui*) and imperative hyphenation (*donne-le-moi*)
skew B1. The item's own `level` column (per-row, e.g. `'B1'`) governs *when the
item is served* in the daily session; the concept band governs *CEFR rollup*.
These are independent (same as the SB Pronoms-Compléments corpus, which sits at
`level='B1'` while tagging the A2-banded `pronoun_placement`). No change needed —
just noting the intended split so it isn't "fixed" later by mistake.

---

## 7. Definition of Done (Content Batch Protocol §⑧ mapping)

This activity ships content, so the four gates apply to its first batch:

- **Gate 1 — Merge confirmed:** `git merge-base --is-ancestor <batch-branch>
  origin/main`.
- **Gate 2 — Serving-path verified:** confirm `GET
  /student/pronoun-transform/items` returns the batch's rows *with*
  `pronounTransform_2026` present — the failure mode to check is a `level`/
  `is_published` filter the rows don't satisfy, or the Practice-hub entry / route
  not wired (activity registration is exactly the "stale allowlist" trap §⑧ warns
  about).
- **Gate 3 — Batch tagging:** every insert sets `source_batch =
  'pronounTransform_2026'`.
- **Gate 4 — Live confirmation:** `GET /version` SHA is an ancestor of the
  deployed build **and** `GET /content-status` shows the `pronoun-transform`
  activity / `pronounTransform_2026` batch with the expected row count. (Extend
  `/content-status` to count the new table — small, and required by Gate 4.)

---

## 8. Effort summary & build order

| Piece | Effort | Risk |
|---|---|---|
| `migration_pronoun_transform.sql` + `MIGRATION_ORDER` | S | Low (additive, `if not exists`) |
| `src/lib/pronounTransform.js` (normalizer + grader + derivations) + tests | **M** | **Medium** — the normalization rules (§2.2) are the crux; get them under unit test first |
| `GET items` / `POST review` routes | S | Low (copy SB plumbing) |
| `PronounTransform.jsx` + registration + i18n | M | Low |
| DepannagePanel `strike` extension (only if annotation ships) | S | Low (additive) |
| `pronounTransformBaseSentences.js` (authored corpus) | **M–L** | Content quality — human-review each canonical |
| `generate-pronoun-transform.js` + deriver/validators | S–M | Low (deterministic, dry-run first) |
| `ACTIVITY_SKILL_MAP` entry + `/content-status` count | S | Low |

**Recommended order:** (1) `pronounTransform.js` normalizer+grader **under test**
(de-risks the whole activity); (2) migration + table; (3) routes; (4) a tiny
hand-seeded batch (5–10 items) to exercise end-to-end; (5) frontend page +
registration; (6) generation script + full authored corpus; (7) annotation
display last (optional polish); (8) run the four gates.

**Explicitly deferred:** any LLM in the grading path (reserved fallback only);
`en_to_fr` direction; tile-based input (that's a different activity and violates
the "type it" requirement).

---

## Appendix — files this plan touches or mirrors

**New**
- `concordance-api/db_migrations/migration_pronoun_transform.sql`
- `concordance-api/src/lib/pronounTransform.js` (+ `.test.js`)
- `concordance-api/src/data/pronounTransformBaseSentences.js`
- `concordance-api/scripts/generate-pronoun-transform.js`
- `concordance-platform/src/pages/PronounTransform.jsx`

**Modified**
- `concordance-api/src/routes/student.js` (two routes)
- `concordance-api/scripts/migrate.js` (`MIGRATION_ORDER`)
- `concordance-api/src/lib/skillTracing.js` (`ACTIVITY_SKILL_MAP`)
- `concordance-api/src/routes/admin.js` or `/content-status` (Gate-4 count)
- `concordance-platform/src/App.jsx`, `src/pages/student/Practice.jsx`
- `concordance-platform/src/locales/{fr,en}/translation.json`
- `concordance-platform/src/components/DepannagePanel.jsx` (only if `source_annotation` ships)

**Mirrored (patterns copied, not changed)**
- `student.js` `POST /sentence-builder/:id/review` — SM-2/BKT plumbing
- `src/lib/sentenceBuilderGeneration.js` + `generate-sentence-builder-accord-pronoms-complements.js` — pure-derivation + dry-run + generation-run pattern
- `migration_sentence_builder.sql` / `migration_sentence_builder_pronoun_placement_concept.sql` — table + concept conventions
- `src/pages/SentenceBuilder.jsx` — page skeleton
- `CONTRIBUTING.md` §⑧ — Definition of Done

---

# Addendum A — Progressive grading strictness + elision normalization

**Status:** Design addendum. Still design-only, no implementation.
**Date:** 2026-07-16
**Amends:** §2 (grading) and §3.2 (review response) of this document.

Two changes: (A.1) make BKT/SM-2 credit eligibility per feedback tier vary by
item level instead of crediting only Tier 1 uniformly; (A.2) an explicit
correction to the §2.2 normalizer — mandatory clitic **elision** (m'en, l'y,
t'en, s'y, …) is **not** fully handled today and needs a dedicated step.

---

## A.1 Progressive grading strictness

### A.1.1 What each tier represents linguistically (the basis for the mapping)

The four tiers from §2.3, restated as *what the student demonstrated about the
`pronoun_placement` skill*:

- **Tier 1 — exact.** Selection ✓, order ✓, orthography ✓. Full demonstration.
- **Tier 2 — accent/spelling near-miss.** Selection ✓, order ✓; the only error is
  **orthogonal to the pronoun skill** (an accent or a verb-spelling slip on
  non-pronoun material). This is a *stronger* pronoun demonstration than Tier 3,
  because order — a genuine part of placement — is correct.
- **Tier 3 — right pronouns, wrong order.** Selection ✓ (the student correctly
  resolved COD/COI, gender/number — the *substitution* logic), but the clitic
  **sequence** is wrong (e.g. *lui le* for *le lui*).
- **Tier 4 — wrong pronouns / other.** Selection ✗. The foundational step failed.

Invariant this forces: credit(Tier 2) ⊇ credit(Tier 3) at every level (Tier 2 is
never a weaker demonstration than Tier 3), and Tier 1 always credits, Tier 4 never.

### A.1.2 Three strictness bands, derived from the item's `level`

```
strictnessBandForLevel(level):
  Band A  (novice, single-pronoun)      ← N1,N2,N3,N4 | A1,A2
  Band B  (intermediate, order is the   ← I1,I2,I3    | B1
           thing being taught)
  Band C  (advanced, integration under  ← I4,Adv      | B2,C1,C2
           production load)
```

(The corpus mixes ACTFL bands and CEFR strings in the `level` column — see the
SB Pronoms-Compléments rows carrying `level:"B1"` — so the helper maps both.)

### A.1.3 The credit-eligibility mapping

| Tier | **Band A** — single pronoun | **Band B** — multi-pronoun, rule *being taught* | **Band C** — multi-pronoun *under load* |
|---|---|---|---|
| **1 — exact** | ✅ credit | ✅ credit | ✅ credit |
| **2 — accent/spelling near-miss** | ❌ no credit | ✅ credit | ✅ credit |
| **3 — right pronouns, wrong order** | ❌ (≈ cannot occur) | ❌ no credit | ✅ credit |
| **4 — wrong pronouns / other** | ❌ | ❌ | ❌ |

### A.1.4 Justification, tier by tier — *why each cell*, not just "more lenient higher up"

**Tier 4 — never credits, any level.** Choosing the wrong clitic (e.g. *la* for
*lui*: a COD/COI confusion) is a failure of the foundational substitution step,
not a slip. There is no level at which the wrong pronoun demonstrates the skill.
Firm floor.

**Tier 2 — credits at B and C, not at A.** An accent/verb-spelling slip is
orthogonal to `pronoun_placement`, so on the pronoun skill *alone* it looks
credit-worthy everywhere. It is withheld at **Band A** deliberately: Band A items
are single, short substitutions where (i) exact reproduction is a fair bar
because the answer is tiny, (ii) orthographic precision is itself part of the
novice target, and (iii) with almost no non-pronoun surface area, a slip is a
weak-but-real signal of shakiness rather than incidental noise. At **Band B/C**
the sentence carries real non-pronoun orthographic surface (verb morphology,
accents on content words); penalizing a stray accent there as if it were a
placement failure *mismeasures* the skill — the pronoun evidence (selection +
order both correct) should dominate. This is exactly the "production load" point:
more surface → more incidental, non-diagnostic slips.

**Tier 3 — credits only at Band C. This is the load-bearing decision, so the
justification is psychometric, not just pedagogical.**
- **Band A:** a single clitic has no order, so Tier 3 essentially *cannot occur*;
  if it does (a spurious extra pronoun), it is noise → no credit.
- **Band B:** B1 multi-pronoun items are precisely where the clitic-order rule is
  *first taught*. Wrong order at B1 most likely means *the ordering rule is not
  yet learned* — which is the very thing the item introduces. Crediting it would
  reward missing the item's teaching point. In BKT terms: at B1 the **prior that a
  wrong-order response is a "slip" (knew-it-but-erred) rather than a "gap"
  (not-learned) is low**, so the honest observation is negative. No credit; the
  student sees the order feedback and the skill belief correctly registers the gap.
- **Band C:** at B2+ the ordering rule is *assumed known* — it was the B1 target —
  and the item's difficulty is dominated by integrating it under load (negation,
  imperative hyphenation, register, longer clauses). A student who selects the
  right clitics but slips the sequence there has shown the core competence, and
  the **prior flips: a wrong-order response at B2 is far more likely a working-
  memory slip on a known rule than a fresh gap.** BKT already models a per-skill
  *slip* probability for "knew it, erred"; crediting Tier 3 at Band C is simply
  asserting that the slip prior — not the not-learned prior — governs here. That
  is the principled reason the *same* response (right pronouns, wrong order) is
  partial-credit at B2 but not at B1: the level changes the prior on slip-vs-gap,
  not the response.

So this is not "leniency drifting upward"; each cell tracks whether, *at that
level*, the observed error is more probably a performance slip on a known
sub-skill or an absence of it — which is what a BKT observation is supposed to
encode.

### A.1.5 Plumbing — how "credit eligibility" reaches BKT and SM-2

Keep BKT binary and SM-2 aligned; introduce **one new flag** distinct from
`correct`:

- `correct` (response field) stays **true only for Tier 1** — the UI must not tell
  a student "parfait" when they had an accent slip or a mis-order.
- `creditEligible = tierCreditsAtBand(tier, strictnessBandForLevel(item.level))`
  — the table above.
- **BKT:** `recordSkillObservation(userId, 'pronoun_placement',
  creditEligible && !revealed, 'pronoun-transform')`. (`&& !revealed` unchanged
  from §2.5 — opening help still forfeits.)
- **SM-2 quality:**
  ```
  quality = revealed        ? QUALITY.difficile        // 2, forfeit
          : tier === 1      ? QUALITY.correct          // 4, clean pass
          : creditEligible  ? QUALITY.presque          // 3, credited near-miss (see below)
          :                   QUALITY.difficile         // 2, no credit → lapse
  ```

**Optional refinement — a third SM-2 quality `QUALITY.presque = 3`.** Today
`srs.js` exposes `{ difficile: 2, correct: 4, facile: 5 }`. A credited *near-miss*
(Tier 2/3 that earns credit at its band) is honestly neither a clean pass nor a
lapse. Quality `3` fits it exactly: in `reviewItem`, `quality < 3` is false so the
**interval is not reset** (it counts, the card advances), while the ease update
`ef + (0.1 − (5−3)(0.08 + (5−3)·0.02)) = ef − 0.14` *lowers* ease slightly, so the
imperfect answer schedules forward but comes back a bit sooner than a clean pass.
This keeps "counted but imperfect" out of both the clean-pass and the lapse
buckets. If added, `QUALITY.presque` also goes into `isValidQuality`. If we prefer
zero `srs.js` change for v1, credited near-misses simply use `QUALITY.correct` (4)
and only BKT/`correct` carry the distinction — acceptable, but the interval then
treats a near-miss identically to a clean pass. **Recommend adding the `3`.**

### A.1.6 Response shape delta (amends §3.2)

Add two fields; everything else in §3.2 stands:
```json
{ "correct": false, "creditEligible": true, "tier": 3, "feedbackCode": "wrong_order_credited", ... }
```
`feedbackCode` gains two credited-near-miss variants
(`accent_near_miss_credited`, `wrong_order_credited`) so the UI can say
"Compté — mais surveillez l'ordre / les accents" (credited) vs the existing
uncredited wording at Band A/B. Localized via i18n as before.

### A.1.7 Authoring & review implications

- The `pronouns[]` count and the item's `level` must be **consistent**: a Band A
  (`level` N1–N4/A1–A2) item with two pronouns is a mis-band, since Band A's
  strictness assumes single-pronoun. Add a generation-script validator (§5.1):
  *`pronouns.length > 1` ⇒ level must resolve to Band B or C.* This prevents a
  multi-pronoun item silently getting Band-A strictness (which, having no Tier-3
  credit, would be defensible but is not the intended calibration).
- No schema change: strictness is a pure function of the existing `level` column
  and the tier, computed server-side at review time.

---

## A.2 Elision normalization — confirmation and required fix

**Confirmation: no, the §2.2 pipeline does NOT fully handle en/y (and me/te/se)
elision. It needs an explicit step.**

What §2.2 step 4 does today is **apostrophe *tightening*** — it unifies apostrophe
glyphs and removes whitespace around an apostrophe that is *already present*
(`l ' aime` → `l'aime`). So when **both** the canonical and the student write the
correctly-elided form (`m'en`, `l'y`, `t'en`, `s'y`, `j'en`, `n'y`), they already
match — that case is fine.

The gap is **un-elided student input**. Elision before a vowel/mute-h is
*mandatory* in French, so a student may still type the un-elided form (`me en`,
`le y`, `se y`). Under the current pipeline:
- Core comparison fails Tier 1 (`me en` ≠ `m'en`), and
- worse, the Tier-3 clitic extractor `P()` lists `me` and `m'` as **separate**
  inventory tokens, so `P("me en") = {me, en}` ≠ `P("m'en") = {m', en}` — the
  un-elided answer wrongly falls through to **Tier 4** ("wrong pronouns"), even
  though the pronouns are right. That is a real mis-grade, not a cosmetic one.

### A.2.1 Fix — add Step 4.5: mandatory clitic elision (forward)

Insert into the §2.2 pipeline, **after** apostrophe tightening (4) and **before**
tokenization / `P()` extraction:

> **4.5 — Mandatory clitic elision (forward).** For each eliding clitic in the
> closed set **{ je, me, te, se, ce, le, la, ne, de, que }** immediately followed
> by a vowel-initial (`a e i o u à â é è ê ë î ï ô û ù` + `y`) or mute-h token,
> drop the clitic's final vowel and join with an apostrophe: `me en → m'en`,
> `te en → t'en`, `se y → s'y`, `le y → l'y`, `la y → l'y`, `je en → j'en`,
> `ne y → n'y`. Apply left-to-right so chains collapse (`ne me en → ne m'en`, and
> `me` elides while `ne` does not before a consonant-initial `me`).

Why **forward** elision (elide the input) rather than **de-elision** (expand
`l'` → `le`/`la`): de-elision is ambiguous (`l'` could be `le` or `la`), whereas
forward elision is not — both `le y` and `la y` correctly collapse to `l'y`, which
is exactly what the canonical stores. So the input converges onto the canonical's
surface unambiguously.

Clitics that **do not** elide are deliberately excluded from the set — `les`,
`lui`, `leur`, `nous`, `vous`, `y`, `en` keep their form before a vowel
(`les y`, `lui en`, `leur en`, `nous en`, `vous en`, `y en` stay two tokens).
Encoding the eliding set as a closed list is what keeps this from over-applying.

Mute-h edge (`l'habille`): elision also occurs before *mute* h but not *aspirate*
h (`le héros` stays). Distinguishing them needs a lexicon; since the corpus is
authored and object-pronoun-before-verb aspirate-h cases are vanishingly rare,
Step 4.5 keys on vowel-initial plus a **small mute-h allowlist seeded from the
verbs actually used in the corpus**, and any residual edge is handled by an
authored `accepted_variants` entry. This mirrors how the rest of the plan pushes
rare irregularities to authoring rather than to runtime heuristics.

### A.2.2 `P()` inventory update

With Step 4.5 in front of it, `P()` sees already-elided input, so the elided
forms are the canonical tokens. Make the inventory list them explicitly and treat
each elided form as **equivalent to its base** for the multiset compare
(so order-only errors are still caught, not masked by an elision mismatch):

```
me≡m'   te≡t'   se≡s'   le/la≡l'   je≡j'   ne≡n'   que≡qu'   de≡d'
plus the non-eliding: les, lui, leur, nous, vous, y, en
```

### A.2.3 Net effect on the tiers

After 4.5: `m'en / l'y / t'en / s'y` written either way (elided or un-elided)
land in **Tier 1** (correct) — elision, like the imperative hyphen (§2.2 step 7),
is a mechanical orthographic rule that should not gate *pronoun-placement*
correctness. If we want to still *nudge* on a missing mandatory elision (parallel
to the hyphenation nudge), compare the pre-4.5 and post-4.5 forms and, when they
differ, append "élision obligatoire : m'en, pas « me en »" to otherwise-correct
feedback — credit unaffected. Recommend the nudge for the same reason as
hyphenation: correct the orthography without penalizing the concept.

### A.2.4 Unit-test obligations (add to `pronounTransform.test.js`, §5.2)

- `m'en` == `me en`, `l'y` == `le y` == `la y`, `t'en` == `te en`, `s'y` == `se y`
  all normalize equal (Tier 1).
- Non-eliding neighbors unchanged: `les y`, `lui en`, `leur en`, `y en`, `vous en`
  do **not** get an apostrophe.
- `P()` multiset: `P("me en") == P("m'en")`; a real order error like
  `lui le` vs `le lui` still differs (Tier 3 still fires).
- Aspirate-h guard: whatever corpus verbs are on the mute-h allowlist elide; a
  control aspirate-h token does not.
