# Roadmap

This roadmap is the honest version of **what sponsorship funds**.

Concordance is built and maintained by a practicing AP French teacher outside the school day. Sponsorship does not purchase a promised feature date. It keeps the platform operating, creates room for careful development, and helps protect free or low-cost access for learners and public-school educators.

Tier details and benefits live on the **[GitHub Sponsors page](https://github.com/sponsors/monsieur-trenton)**, which is the source of truth.

---

## The immediate funding goal

The first objective is not rapid growth or replacing a teaching salary.

It is to make Concordance operationally sustainable:

- keep hosting, storage, databases, monitoring, and email running;
- cover speech recognition and text-to-speech usage;
- cover carefully controlled AI inference for feedback and conversation;
- maintain security, privacy, accessibility, and backups;
- support a small number of authentic classroom and independent-learner pilots;
- create enough development margin to improve the platform without rushing.

The project will publish real operating-cost ranges once usage is stable enough for those numbers to be meaningful. Until then, this roadmap will not present invented or placeholder figures.

## Cost discipline

Concordance should not become dependent on an unlimited stream of API calls.

LAOS is being designed to reduce variable cost through:

- deterministic evidence and framework logic;
- event-driven learner-state updates;
- cached and precomputed recommendations;
- model routing by task complexity;
- specialized speech services only where speech adds educational value;
- external generative AI only when it contributes something the evidence system cannot provide reliably.

The long-term affordability principle is simple:

> **Reason from evidence first. Generate only when generation adds pedagogical value.**

This work makes sponsorship go further and makes future school adoption more realistic.

## Architectural foundation completed

Concordance has completed the accepted architectural design needed to connect learner performance with future individualized educational guidance:

- evidence-centered learner modeling with traceable provenance;
- controlled, reproducible processing of learner-state changes;
- independent interpretation through proficiency frameworks; and
- a governed, explainable Recommendation Engine architecture.

This foundation separates educational evidence, learner understanding, framework interpretation, and instructional guidance so that the system can be audited, replayed, and improved without making AI the educational authority.

The Recommendation Engine runtime is not yet implemented. The next engineering phase is to build that runtime; the Individual Learner Path is a subsequent learner-facing direction that will turn advisory educational intelligence into choices among meaningful learning opportunities. Neither is presented here as a completed product capability.

---

## Current product focus

### 1. Learner Evidence Profile

The flagship teacher experience should make a learner's development understandable from one coherent screen:

- instructional level target;
- evidence-informed ACTFL and CEFR portrait;
- evidence strength and gaps;
- grammar, thematic vocabulary, and communication needs;
- provenance for the work supporting each conclusion;
- teacher contestation without destructive overwrites.

### 2. Individual Learner Path — upcoming

The future Individual Learner Path should give independent learners useful, evidence-derived opportunities without requiring a classroom roster:

- recommended proficiency focus;
- grammar points supported by observed performance;
- thematic vocabulary practice that needs expansion;
- communication tasks chosen to gather missing evidence;
- clear evidence gaps rather than fabricated certainty;
- read-only access to provenance.

It should preserve learner choice and pace, teacher guidance where available, and a clear reason for each opportunity. It is not intended to become a fixed adaptive course or a prediction of how quickly a learner will progress.

### 3. AP French and AAPPL preparation

Concordance's first public-impact focus remains:

- AP French Language and Culture preparation;
- AAPPL preparation;
- support for learners pursuing State Seals of Biliteracy;
- communicative performance across interpretive, interpersonal, and presentational modes;
- guidance tied to proficiency evidence rather than practice completion alone.

### 4. Speaking evidence

Speaking is essential to communicative proficiency and cannot be inferred responsibly from writing alone. Current and planned work includes:

- reliable transcription through specialized speech recognition;
- pronunciation and fluency evidence;
- graded speaking tasks;
- conversational elicitation of missing evidence;
- careful separation between raw transcription, evidence assertions, and proficiency interpretation.

### 5. Lower-cost infrastructure

Before broad expansion, Concordance will continue improving:

- API budgets and usage telemetry;
- per-feature cost attribution;
- caching and invalidation rules;
- provider fallback without duplicate billing;
- rate limits and abuse protection;
- background computation triggered by new evidence rather than repeated dashboard views.

---

## Research and validation

Concordance is open to research partnerships in second-language acquisition, applied linguistics, assessment, learning sciences, and responsible educational AI.

A future research collaboration should preserve these boundaries:

- LAOS and other pre-existing intellectual property remain under their current ownership;
- research questions, evaluation methods, and findings can be published;
- participant consent, student privacy, and institutional review requirements are respected;
- publication rights and software rights are negotiated separately;
- no university or funder receives ownership of LAOS merely by studying or piloting Concordance.

Potential early studies include:

- whether the Learner Evidence Profile improves teacher decision-making;
- whether learners understand their proficiency and next steps more clearly;
- whether evidence-driven practice improves AP French or AAPPL readiness;
- whether deterministic LAOS reasoning reduces AI cost without reducing educational usefulness;
- whether contestable evidence improves trust in AI-supported assessment.

---

## Longer-term direction

### Broader Francophone representation

Expand listening, reading, and cultural content across Francophone communities, registers, and perspectives rather than treating Metropolitan French as the entire language ecosystem.

### Additional languages

Extend LAOS only with qualified language and pedagogy collaborators. Spanish is a natural candidate, especially where the system can model cross-linguistic transfer, cognates, false friends, and structural interference rather than creating a disconnected copy of the French product.

### Future platform capabilities

As the foundation develops, downstream capabilities may include a Teacher Workspace for reviewing evidence and guiding priorities, a Media Center for connecting educational targets with authentic audio, video, literature, and cultural material, and a Language Specialist Workspace for maintaining living language and pedagogical definitions. These are planned directions, not current claims about completed work.

The same architecture may support a governed Research Platform for studying proficiency development, individualized learning, transfer, and recommendation calibration using appropriate consent, privacy, and institutional governance. It may also eventually contribute to language documentation and preservation partnerships, including with Indigenous and endangered-language communities, only where community authority, consent, stewardship, and ownership remain non-negotiable. This is a long-term aspiration, not a current product capability.

### LAOS as a licensable platform

If LAOS demonstrates reliable educational value, future possibilities include:

- a hosted API for approved educational partners;
- institutional or research licenses;
- integrations with assessment, curriculum, or learning platforms;
- commercial licensing that helps subsidize learner and teacher access to Concordance.

Licensing is a future option, not a reason to weaken the current educational mission.

### Sustainable stewardship

The desired outcome is a durable educational craft business:

- the creator can continue teaching;
- operating expenses are covered;
- contributors can be compensated when resources allow;
- development remains careful and mission-aligned;
- recurring revenue can eventually provide meaningful family support;
- Concordance can outlast changes in individual AI vendors or models.

---

## What has already been built

The platform already includes substantial work across placement, adaptive practice, conversation, pronunciation, listening, writing, teacher analytics, AP French preparation, AAPPL-oriented expression tools, privacy, and safety.

For a current visual overview, see **[FEATURES.md](FEATURES.md)**. For dated engineering progress, see **[UPDATES.md](UPDATES.md)**.

---

## Support or participate

- **[Sponsor the project](https://github.com/sponsors/monsieur-trenton)** to help cover infrastructure and responsible development.
- **[Open an issue](https://github.com/monsieur-trenton/concordance/issues/new/choose)** with a pilot, research, partnership, or feature idea.
- Review **[CONTRIBUTING.md](CONTRIBUTING.md)** for ways to help while the production core remains private.
