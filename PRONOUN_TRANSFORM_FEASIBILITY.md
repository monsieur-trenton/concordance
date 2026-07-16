# Pronoun-Transformation Exercise — Feasibility Comparison

**Status:** Investigation only. No code was changed and nothing was built.
**Audience:** Product decision input, not a final answer.
**Date:** 2026-07-16

---

## The idea being scoped

A new practice activity where the student sees a full sentence
(e.g. *« Je donne le livre à Marie »*) and must **actively produce the
pronoun-transformed version** (e.g. *« Je le lui donne »*) — typing/constructing
the whole transformed sentence, **not** selecting from options and **not**
filling a single blank. The original pitch was to *extend Dépannage's existing
bold-span highlighting* to also show which words moved/disappeared and why.

Two questions decide feasibility:

1. **Input** — how does the student produce a full multi-word sentence?
2. **Validation** — how do we grade a constructed sentence (with legitimate
   French pronoun-order flexibility) and show what should have changed?

Everything below is grounded in the current code.

---

## What exists today (the load-bearing facts)

### Dépannage is a *display* component, not an input or a grader

`concordance-platform/src/components/DepannagePanel.jsx` (87 lines) renders
already-resolved strings: a FR/EN header toggle, an optional
`correctedSentence`, an optional `specificFeedback`, and an `explanation`. It
has **no text input, no onChange, no submit, no validation** — its own header
comment says *"this component only renders already-resolved strings."* It is
mounted purely as post-answer feedback by `MultipleChoiceCard.jsx`,
`ExerciseCard.jsx`, `AssignmentMode.jsx`, and the Sentence Builder page.

The **bold-span infrastructure** is the `correctedSentence` shape:

```js
correctedSentence: [{ t: "Je " }, { t: "lui", bold: "pronom" }, { t: " ai parlé." }]
```

Critically, `bold` is **decorative only**. The renderer is:

```js
// DepannagePanel.jsx
{segments.map((seg, i) => seg.bold
  ? <strong key={i}>{seg.t}</strong>
  : <span key={i}>{seg.t}</span>)}
```

The category (`"subject" | "verb" | "pronom" | "antecedent" | "qui"`) is a
documented authoring convention but is **thrown away at render** — every value
just yields `<strong>`. So the current infra can **bold the final pronoun in the
correct answer**. It does **not** encode or visualize *movement*, *deletion*, or
*why* anything changed. There is no diff, no alignment between the original and
transformed sentence, no strike-through, no arrows.

### How answers are graded today — exact match everywhere

Every **discrete graded item** is exact-match:

| Path | File | Grading |
|---|---|---|
| Legacy card check | `checker.js` | `normalize(input) === normalize(answer)` (accent-insensitive) |
| Vocab production | `vocabSession.js` `checkProductionAnswer` | exact normalized (accent-**sensitive**) |
| Main exercise check | `student.js` `POST /results/check` | exact normalized; multi-blank = per-blank exact match |
| Sentence Builder | `student.js` `POST /sentence-builder/:id/review` | assembled **tile-ID sequence** equals stored order |

There is **no** fuzzy matching, no edit-distance, no "accepted variants" list
anywhere in the discrete-item pipeline. Sentence Builder sidesteps free-text
grading entirely by constraining the student to **tiles** and comparing tile IDs.

### Where fuzzy / free-text grading *does* exist — the open-ended world

LLM-based evaluation exists, but only for **open-ended production**, never for a
BKT-feeding discrete item:

- `conversationScoring.js` `scoreConversation` — holistic rubric (Fluency /
  Accuracy / Complexity), Claude Sonnet, for Speaking/Conversation.
- `student.js` `POST /analyze-essay`, `POST /analyze-writing` — holistic essay
  scores + feedback.
- `student.js` `POST /proofread-writing` — **the most relevant existing
  pattern.** It asks Claude to return position-anchored errors with a *verbatim*
  substring, then locates each with `text.indexOf(errorText, cursor)` to get
  exact offsets, plus `correction`, `explanation`, and `severity`, and feeds them
  into `error_tags → BKT/SRS`. This is essentially "grade free text and say what
  should have changed, with spans."

So: the building blocks for grading a typed sentence **and** for "what should have
changed" feedback already exist — but they live in the writing/speaking surface,
not in the discrete practice-item surface.

### The activity registration surface is modest

A new practice activity is wired in a handful of known places (Sentence Builder is
the worked example):

- `src/App.jsx` — one lazy import + one `<Route>` (`/student/practice/...`).
- `src/pages/student/Practice.jsx` — one entry in `getCategoriesConfig()`.
- `concordance-api/src/lib/skillTracing.js` — one `ACTIVITY_SKILL_MAP` entry.
- One `grammar_concepts` row (the BKT skill key; `pronoun_placement` **already
  exists** — seeded in `migration_sentence_builder_pronoun_placement_concept.sql`).
- i18n label keys.

---

## PATH A — Extend Dépannage

### 1–2. What Dépannage does today
Pure highlighting/annotation display (see above). It renders resolved strings and
bolds authored spans. It collects no input and makes no judgment.

### 3. What would have to change
- **(a) Full-sentence input** — net-new. Dépannage has no input surface at all.
  You'd be adding a textarea + submit + state, i.e. a new component; Dépannage
  can't host it without ceasing to be Dépannage.
- **(b) Validation with valid variants** — net-new. Nothing in the Dépannage/
  exercise path does multi-word or multi-variant matching. Dépannage never
  touches correctness; it only shows the answer after the fact.
- **(c) "What moved/changed" visualization** — net-new. Bold spans bold; they do
  not diff. Showing removal of *« le livre »* / *« à Marie »* and insertion of
  *« le lui »* requires an original↔transformed alignment the current shape does
  not carry.

### 4. Verdict on Path A
**It's effectively a new build wearing Dépannage's clothing.** The only part of
the task Dépannage genuinely covers is the *feedback panel* — reusing
`correctedSentence` to show the correct transformed sentence with the pronouns
bolded (free, and worth doing). Input, validation, and movement-visualization all
live outside it. "Extend Dépannage" as literally framed conflates the reusable
feedback renderer with the actual work, which is a new activity.

---

## PATH B — New activity type

### 1. What a minimal new activity requires
There is a clean, recent precedent to copy: **Sentence Builder**. It is exactly
this shape (assemble a French sentence, feed BKT/SRS, reuse DepannagePanel for
feedback), so the skeleton is known-good:

- **Frontend component** — new page modeled on `SentenceBuilder.jsx`
  (prompt → input → submit → DepannagePanel feedback). Reuses DepannagePanel.
- **Backend** — a route pair like Sentence Builder's `GET items` /
  `POST :id/review`, decided server-side so answers aren't leaked; write to
  `activity_progress` and `recordSkillObservation` exactly as it already does.
- **Storage** — extend/mirror `sentence_builder_items` (a bounded-JSONB item
  table) rather than a brand-new schema; it already carries `prompt`,
  `corrected_sentence`, `level`, `source_batch`, and a concept FK.
- **Generation** — a `generate-sentence-builder-*.js`-style script +
  `sentenceBuilderGeneration.js` helpers; the AI-authoring + `--sample`/`--dry-run`
  pipeline pattern is established.
- **Wiring** — the modest registration surface listed above.

### The real fork is input modality → which decides grading

- **B1 — tile assembly (reuse Sentence Builder wholesale).** Deterministic
  tile-ID grading, near-zero new risk. **But this violates the product
  requirement** ("not select from options … construct/type the entire
  transformed sentence"). Tiles are selection in spirit. Cheap, but not the
  pitched activity.
- **B2 — free typed sentence (meets the requirement).** Needs new grading. Two
  sub-approaches:
  - **B2-deterministic:** canonical answer + a curated list of accepted variants,
    with normalization (case, spacing, apostrophes `l'`, hyphens `donne-le-moi`).
    Feasible because the pronoun-transform answer space is **small and
    rule-governed** — legitimate flexibility is bounded (e.g. placement before an
    infinitive *« je vais le lui donner »*, affirmative-imperative hyphenation).
    This is the **first discrete activity to need multi-variant matching**, but
    it's a small, well-scoped extension of the existing exact-match check (store
    an array of acceptable answers; return correct if the normalized input matches
    any). No AI in the grading loop → keeps BKT signal deterministic.
  - **B2-AI-graded:** reuse the `proofread-writing` pattern to judge equivalence
    and emit the "what moved/changed" explanation. Most flexible, and gives the
    movement feedback almost for free — but adds latency, cost, and
    nondeterminism to a BKT-feeding item, which is overkill for a constrained
    transform and a departure from how discrete items are scored today.

### 2. Effort / risk vs Path A
Path B is **more honest and not much more work** than a truthful Path A, because a
truthful Path A collapses into Path B anyway (new input + new validation + new
visualization, with Dépannage reused for feedback). Path B additionally gets a
proven skeleton (Sentence Builder) to copy, whereas "extend Dépannage" has no
precedent for hosting input.

---

## Validation challenge — direct answer

> Does any existing activity do fuzzy/multi-word answer matching, or is this the
> first?

- **For discrete graded items: this would be the first.** All discrete grading is
  exact normalized match (`/results/check`, `checkAnswer`, `checkProductionAnswer`)
  or tile-ID equality (Sentence Builder). None accept variants or do fuzzy/
  multi-word matching.
- **But the codebase is not starting from zero.** `proofread-writing` already does
  LLM span-anchored correction with verbatim-substring location, corrections, and
  BKT/SRS feed-in; `scoreConversation` / `analyze-writing` already do holistic
  free-text scoring. These are directly borrowable — the gap is that they live in
  the writing/speaking surface, and none of them is a deterministic discrete-item
  grader.

For a rule-governed transform, **B2-deterministic (canonical + curated variants)**
is the smallest thing that meets the product requirement and keeps grading
deterministic; the `proofread-writing` LLM pattern is the ready fallback if the
variant space proves larger than expected or if the "explain what moved" feedback
needs to be generated rather than authored.

---

## The "what moved / why" feedback — neither path gets it free

Bold spans do not diff. To show *« le livre »* and *« à Marie »* being removed and
*« le lui »* inserted, you need an original↔transformed alignment. Two options:

- **Authored dual annotation (pragmatic):** store both the original (with
  strike-through spans on removed noun phrases) and the transformed (with bolded
  pronouns) — a small display built alongside the existing `correctedSentence`
  shape, authored/generated with the item. Deterministic, no runtime AI.
- **Generated (proofread-style):** have the model emit the moved/removed/inserted
  structure. Flexible, but runtime cost + nondeterminism.

Either way it is **new display work** on top of the current bold-only renderer.

---

## Recommendation (input for the product decision)

1. **Frame it as a new activity (Path B), modeled on Sentence Builder**, reusing
   `DepannagePanel` for the feedback panel. Treat "extend Dépannage" as "reuse
   Dépannage's corrected-sentence display inside a new activity," not as the
   architecture — Dépannage has no input/validation responsibility to extend.
2. **Decide the input modality first — it's the whole ballgame.** If the product
   truly requires *typed* full sentences, commit to **B2**. If tile-assembly is
   acceptable, **B1** is dramatically cheaper and near-zero risk (but it's not the
   pitched "construct/type" experience).
3. **For B2 grading, start deterministic** (canonical + curated accepted variants
   + normalization). It's the first discrete activity to need this, but it's a
   small, contained extension, and the pronoun-order answer space is enumerable.
   Keep the `proofread-writing` LLM pattern in reserve for equivalence-judging or
   for generating the "what moved" explanation.
4. **Budget the movement visualization as net-new** regardless of path; prefer
   authored dual annotation over runtime AI for a deterministic, cacheable result.

**One-line takeaway:** Dépannage can supply the *feedback surface* and should be
reused, but the input, the validation, and the movement visualization are all new
— so this is a **new activity type (Path B)** that borrows Dépannage's display and
Sentence Builder's skeleton, with a deliberate up-front choice between typed-input
(meets the pitch, needs first-of-its-kind variant grading) and tile-input (cheap,
but not the pitched experience).

---

## Appendix — key files reviewed

**concordance-platform (frontend)**
- `src/components/DepannagePanel.jsx` — display component + `correctedSentence` shape
- `src/components/MultipleChoiceCard.jsx`, `ExerciseCard.jsx`, `AssignmentMode.jsx` — Dépannage consumers; single/multi-blank input
- `src/components/PronomsExerciseCard.jsx` — existing single-pronoun exercise (typed, exact-match)
- `src/pages/SentenceBuilder.jsx` — tile-assembly activity (Path B precedent)
- `src/utils/checker.js` — exact normalized match
- `src/App.jsx`, `src/pages/student/Practice.jsx` — activity registration surface

**concordance-api (backend)**
- `src/routes/student.js` — `POST /results/check`, `POST /sentence-builder/:id/review`, `POST /proofread-writing`, `POST /analyze-writing|-essay`
- `src/lib/vocabSession.js` — `checkProductionAnswer` / `normalizeFrenchAnswer`
- `src/lib/conversationScoring.js` — LLM rubric scoring
- `src/lib/sentenceBuilderGeneration.js`, `scripts/generate-sentence-builder-accord-pronoms-complements.js` — generation pipeline
- `db_migrations/migration_sentence_builder.sql`, `migration_sentence_builder_pronoun_placement_concept.sql` — item schema + existing `pronoun_placement` concept
- `src/data/accordPronomsComplementsBaseSentences.js` — pronoun item data shape
