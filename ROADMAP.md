# Roadmap

This roadmap is the honest version of **what sponsorship funds**.

Concordance is built and maintained by a practicing AP French teacher outside the school day. Sponsorship does not purchase a promised feature date. It keeps the platform operating, creates room for careful development, and helps protect free or low-cost access for learners and public-school educators.

Tier details and benefits live on the **[GitHub Sponsors page](https://github.com/sponsors/monsieur-trenton)**, which is the source of truth.

---

## The immediate funding goal

The first objective is to make Concordance operationally sustainable: keep hosting, storage, databases, monitoring, and email running; cover speech recognition, text-to-speech, and carefully controlled AI inference; maintain security, privacy, accessibility, and backups; support authentic classroom and independent-learner pilots; and create enough development margin to improve the platform without rushing.

The project will publish real operating-cost ranges once usage is stable enough for those numbers to be meaningful. Until then, this roadmap will not present invented or placeholder figures.

## Cost discipline

Concordance should not become dependent on an unlimited stream of API calls. LAOS is being designed to reduce variable cost through deterministic evidence and framework logic, event-driven learner-state updates, cached and precomputed educational intelligence, model routing by task complexity, specialized speech services only where speech adds educational value, and external generative AI only when it contributes something the evidence system cannot provide reliably.

> **Reason from evidence first. Generate only when generation adds pedagogical value.**

This work makes sponsorship go further and makes future school adoption more realistic.

## Architectural foundation and ILP milestone

Concordance has now implemented a substantial governed path from learner evidence to individual learning opportunities. The architecture separates evidence assertions, canonical learner state, framework projections, recommendations, and the Individual Learner Path so that each layer has a bounded responsibility. Historical educational decisions can be preserved and verified rather than silently recomputed according to whatever model or policy happens to be current.

The Recommendation Engine runtime and the foundational Individual Learner Path runtime are no longer merely planned architecture. The ILP can construct governed individual learning opportunities from upstream educational intelligence while preserving recommendation rank and provenance, teacher authority, learner agency, and learner-owned pace. Governed retrieval is also in place. This is a backend and architectural milestone, not a claim that the complete learner-facing experience is finished.

A bounded append-only learner-interaction obligation remains part of completing the accepted ILP boundary. Concrete activity resolution, launch mechanics, media delivery, grading, and evidence creation remain downstream concerns and will not be folded into ILP simply to make the product appear more complete.

---

## Current product focus

### 1. Learner Evidence Profile

The flagship teacher experience should make a learner's development understandable from one coherent screen, including evidence-informed framework portraits, evidence strength and gaps, grammar and communication needs, provenance for the work supporting each conclusion, and teacher contestation without destructive overwrites.

### 2. Individual Learner Path

The foundational ILP runtime now exists. Its purpose is to turn governed educational intelligence into meaningful opportunities without constructing a compulsory adaptive course or predicting how quickly a learner should progress. Recommendations remain advisory, teacher-required work remains structurally distinct and authoritative, and learner choice does not become evidence of proficiency or motivation merely because it can be recorded.

The remaining work is deliberately bounded: complete the accepted interaction-recording boundary, then connect individual opportunities to useful learner experiences without allowing delivery availability or interface behavior to rewrite educational truth.

### 3. Media Center and authentic culture

The Media Center is the next major platform direction. It will treat authentic texts, audio, video, visual art, and cultural artifacts as first-class resources rather than decorative enrichment. Authentic culture should not be reserved for advanced learners. A beginning learner can encounter an authentic resource when the task and linguistic demand surrounding it are appropriately scaffolded.

The resource itself does not receive a proficiency level. Educational and linguistic demand belongs to the governed task around the resource. Media Center architecture will therefore describe resource identity, provenance, rights, and availability while keeping proficiency interpretation and educational decision-making in their proper layers.

### 4. AP French, AAPPL, and broader proficiency preparation

Concordance's first public-impact focus remains AP French Language and Culture preparation, AAPPL preparation, support for learners pursuing State Seals of Biliteracy, and communicative performance across interpretive, interpersonal, and presentational modes. Guidance should be tied to proficiency evidence rather than practice completion alone.

### 5. Speaking evidence

Speaking is essential to communicative proficiency and cannot be inferred responsibly from writing alone. Current and planned work includes reliable transcription, pronunciation and fluency evidence, graded speaking tasks, conversational elicitation of missing evidence, and careful separation between raw transcription, evidence assertions, and proficiency interpretation.

### 6. Operations Health Center

After the Media Center, Concordance plans an Operations Health Center to make sustainability measurable. It should track API and provider usage, identify unusually expensive usage patterns, estimate per-user and total monthly operating costs, and help enforce budgets and cost discipline without turning learner activity into unnecessary surveillance.

---

## Frameworks and validation

Framework projections remain interpretations of learner state rather than learner identity or official certification. ACTFL and CEFR are central to the current direction, and International Language Roundtable (ILR) support is planned as a future governed framework projection. Concordance may also support preparation contexts such as AP and AAPPL without claiming affiliation with or certification authority from the organizations that maintain those systems.

Concordance is also developing an empirical validation program around proficiency-aligned communicative evidence. Research and validation should test whether the architecture's educational claims are actually supported, not merely demonstrate that the software functions as designed.

## Research and collaboration

Concordance is open to research partnerships in second-language acquisition, applied linguistics, assessment, learning sciences, and responsible educational AI. Research questions, evaluation methods, and findings can be publishable while LAOS and other pre-existing intellectual property remain under their current ownership. Participant consent, student privacy, institutional review requirements, and the distinction between publication rights and software rights must be respected.

Potential studies include whether evidence profiles improve teacher decision-making, whether learners understand their proficiency and next steps more clearly, whether evidence-driven practice improves readiness for proficiency-oriented assessments, whether deterministic LAOS reasoning reduces AI cost without reducing educational usefulness, and whether contestable evidence improves trust in AI-supported assessment.

---

## Longer-term direction

### Broader Francophone representation and language specialists

Concordance should reflect living language rather than treating a textbook register as the entire language. Future stewardship can involve language specialists ranging from professors and linguists to educators, community experts, and younger speakers who understand contemporary usage and slang. Expertise should be matched to the question being answered, with appropriate review and provenance rather than treating any one contributor as universally authoritative.

### Additional languages and preservation

French is the first implementation, not the final scope. Spanish is a natural expansion candidate, and additional languages should be developed with qualified linguistic, pedagogical, and community collaborators. A much longer-term ambition is to contribute to language documentation and preservation partnerships, including Indigenous and endangered languages, only where community authority, consent, stewardship, and ownership remain non-negotiable.

### Institutional and professional contexts

The same governed framework architecture may eventually support learners and institutions working in professional contexts. Planned ILR projection work creates a possible future path toward contexts where ILR proficiency descriptions are relevant, including U.S. government language programs. Any future relationship with organizations such as the U.S. Department of State would require validation, procurement, security, privacy, accessibility, and institutional review and is an aspiration rather than a current partnership or capability.

### LAOS as a licensable platform

If LAOS demonstrates reliable educational value, future possibilities include a hosted API for approved educational partners, institutional or research licenses, integrations with assessment or learning platforms, and commercial licensing that helps subsidize learner and teacher access to Concordance. Licensing is a future option, not a reason to weaken the current educational mission.

### Sustainable stewardship

The desired outcome is a durable educational project that can keep access broad while becoming financially sustainable. Operating expenses should be covered, contributors and language specialists should be compensated when resources allow, development should remain careful and mission-aligned, and recurring revenue can eventually provide meaningful family support. Concordance should be capable of outlasting changes in individual AI vendors or models.

---

## Privacy and intellectual-property stewardship

Privacy is an architectural requirement, not a policy added at the end. Concordance is being designed around data minimization, purpose limitation, learner isolation, human review, and the principle that ordinary interaction behavior should not silently become evidence about motivation, ability, or proficiency. Individualization should not require surveillance.

The public repository intentionally explains Concordance's mission, principles, public architecture, research direction, and progress without publishing the protected implementation details of the LAOS reasoning kernel. The project can open more research, documentation, interfaces, and infrastructure over time while preserving the intellectual property needed to support sustainable stewardship and future licensing.

## What has already been built

The platform already includes substantial work across placement, adaptive practice, conversation, pronunciation, listening, writing, teacher analytics, AP French preparation, AAPPL-oriented expression tools, privacy, and safety. The LAOS pipeline now extends through implemented Recommendation and foundational Individual Learner Path runtimes, with the remaining ILP interaction boundary deliberately separated from downstream delivery work.

For a current visual overview, see **[FEATURES.md](FEATURES.md)**. For dated engineering progress, see **[UPDATES.md](UPDATES.md)**.

---

## Support or participate

- **[Sponsor the project](https://github.com/sponsors/monsieur-trenton)** to help cover infrastructure and responsible development.
- **[Open an issue](https://github.com/monsieur-trenton/concordance/issues/new/choose)** with a pilot, research, partnership, or feature idea.
- Review **[CONTRIBUTING.md](CONTRIBUTING.md)** for ways to help while the production core remains private.
