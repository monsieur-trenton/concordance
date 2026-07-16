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
