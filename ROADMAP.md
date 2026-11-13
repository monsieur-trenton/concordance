# Roadmap

Current-reference review: November 1, 2026. Delivery sequence and feature acceptance criteria updated November 13, 2026. Source milestones do not establish production availability.

This roadmap is the honest version of **what sponsorship funds**.

Concordance is built and maintained by a practicing French teacher outside the school day. Its classroom origins keep the project accountable to real learners without defining Concordance solely by one course or examination. Sponsorship does not purchase a promised feature date. It keeps the platform operating, creates room for careful development, and helps protect free or low-cost access for learners and public-school educators.

Tier details and benefits live on the [GitHub Sponsors](https://github.com/sponsors/monsieur-trenton) and [Patreon](https://www.patreon.com/Concordance_Learn) pages. Each platform is the source of truth for its current offering.

---

## The immediate funding goal

The first objective is to make Concordance operationally sustainable: keep hosting, storage, databases, monitoring, and email running; cover speech recognition, text-to-speech, and carefully controlled AI inference; maintain security, privacy, accessibility, and backups; support authentic classroom and independent-learner pilots; and create enough development margin to improve the platform without rushing.

The project will publish real operating-cost ranges once usage is stable enough for those numbers to be meaningful. Until then, this roadmap will not present invented or placeholder figures.

## Cost discipline

Concordance should not become dependent on an unlimited stream of API calls. LAOS is being designed to reduce variable cost through deterministic evidence and framework logic, event-driven learner-state updates, cached and precomputed educational intelligence, model routing by task complexity, specialized speech services only where speech adds educational value, and external generative AI only when it contributes something the evidence system cannot provide reliably.

> **Reason from evidence first. Generate only when generation adds pedagogical value.**

This work makes sponsorship go further and makes future school adoption more realistic.

## Architectural foundation, ILP, and experience resolution

Concordance has implemented a substantial governed path from learner evidence to bounded learning experiences. The architecture separates evidence assertions, canonical learner state, framework projections, recommendations, the Individual Learner Path, and downstream experience resolution so that each layer has a limited responsibility. Historical educational and resolution decisions can be preserved and verified rather than silently recomputed according to whatever model, policy, or catalog happens to be current.

The Recommendation Engine and Individual Learner Path runtimes are implemented and accepted through their intended boundaries. The ILP can construct governed individual learning opportunities while preserving recommendation rank and provenance, teacher authority, learner agency, and learner-owned pace. Its append-only interaction ledger records permitted learner actions without converting them into proficiency or motivation judgments.

Downstream Experience Resolution is also implemented and accepted for the currently supported Concordance-native practice path. It can bind a verified opportunity to a specific content revision and produce a learner-safe launch projection without reranking recommendations, substituting educational targets, or mutating learner state. This is backend infrastructure, not a claim that every delivery modality or complete learner-facing experience is finished. Authentic media, broader activity families, execution, grading, and new evidence creation remain separately governed concerns.

## Approved delivery sequence

This section defines the production sequence; the product directions below are topics, not a separate release order.

1. complete and qualify a supported Media Center path while public authentic-media access remains closed until its authorized release gate;
2. pass a bounded cleanup and integration gate that reconciles the delivered system with interface work already underway;
3. complete and migrate the total product redesign, including branding and every retained public and authenticated page;
4. deliver the Linguistic Specialist role, onboarding, content and vocabulary authoring, and feature-proposal workflow;
5. deliver the Operations Health Center (OHC); and
6. pilot new learner activities and refinements, including Meaning Across Languages, Contact Zone inquiries, visual grammar, purposeful conversation, and feedback leading to a second attempt.

The isolated redesign and an independent new-learner ILP are already being developed alongside Media Center work. Their construction can continue within their approved scope; cleanup and integration precede production cutover. The redesign includes the Media Center and new-learner ILP pages where applicable. Migration must preserve educational authority, evidence behavior, learner state, privacy boundaries, and accepted LAOS contracts.

Security, reliability, essential provider limits, and basic cost monitoring continue throughout. A concrete urgent operational issue can justify a separately scoped minimal intervention before the full OHC; it does not move the entire dashboard ahead of the Specialist. Design explorations do not require new interactive lessons to ship before the redesign can close.

---

## Product directions within the delivery sequence

### Learner Evidence Profile

The flagship teacher experience should make a learner's development understandable from one coherent screen, including evidence-informed framework portraits, evidence strength and gaps, grammar and communication needs, provenance for the work supporting each conclusion, and teacher contestation without destructive overwrites.

### Individual Learner Path

The foundational ILP runtime now exists. Its purpose is to turn governed educational intelligence into meaningful opportunities without constructing a compulsory adaptive course or predicting how quickly a learner should progress. Recommendations remain advisory, teacher-required work remains structurally distinct and authoritative, and learner choice does not become evidence of proficiency or motivation merely because it can be recorded.

The accepted interaction-recording boundary is now complete. Current work moves downstream: connect opportunities to useful learner experiences while preserving the ILP's educational decisions and keeping delivery availability or interface behavior from rewriting educational truth.

### Media Center and authentic culture

The Media Center is the next major platform direction. Its architecture is accepted. Media Center source implementation now extends beyond retained media-revision identity and bounded database authority into rights-policy, eligibility, availability, deliverability, and resolution-authority foundations. Source presence does not establish completed qualification, deployment, or production activation. No external provider is admitted through the supported path, and public authentic-media resolution and preview remain closed.

The Media Center will treat authentic texts, audio, video, music, journalism, literature, visual art, photography, and other cultural artifacts as first-class resources rather than decorative enrichment. Authentic culture should not be reserved for advanced learners. A beginning learner can encounter an authentic resource when the task and linguistic demand surrounding it are appropriately scaffolded.

The resource itself does not receive a proficiency level. Educational and linguistic demand belongs to the governed task, scaffold, or experience around the resource. Existing policy and authority source still requires the applicable qualification and operational gates. Remaining work must complete downstream selection and delivery, require explicit provider admission, and enforce current eligibility, availability, and deliverability before public access can open.

Concordance is not intended for learners under 13. The approved policy direction requires age declaration at sign-up and restricts Media Center access until age is declared. Media suitability is to be triaged for learners aged 17 and under, adults aged 18 and over, and undetermined age. Age suitability is separate from proficiency. These are approved requirements, not a claim that enforcement has been verified in production.

### Cleanup gate and total product redesign

Before the redesigned interface is migrated into the production application, Concordance will use a cleanup and integration gate to reduce avoidable coupling, confirm supported product paths, and protect the accepted educational boundaries already established in LAOS.

The separate redesign preview currently explores bilingual French and English interfaces, light and dark themes, responsive layouts, clearer navigation, visible display settings, and a more coherent visual language across learner activities. It remains review work rather than a production release. The eventual migration is intended to encompass the full site and shared branding system, not only a new landing page.

### Communicative proficiency and learner goals

Concordance's first implementation is French, with communicative development across interpretive, interpersonal, and presentational modes. Learners may be preparing for academic or professional work, travel, heritage connection, literature and media, relationships, or lifelong learning. Current preparation contexts include AP French Language and Culture, AAPPL, and State Seals of Biliteracy, but those contexts are applications of the proficiency-first platform rather than its identity. Guidance should remain tied to evidence of meaningful language use rather than practice completion alone.

### Speaking evidence

Speaking is essential to communicative proficiency and cannot be inferred responsibly from writing alone. Current and planned work includes reliable transcription, pronunciation and fluency evidence, graded speaking tasks, conversational elicitation of missing evidence, and careful separation between raw transcription, evidence assertions, and proficiency interpretation.

### Linguistic Specialist workspace

The first new capability after redesign migration is a workspace for qualified language and cultural specialists to author content, build vocabulary sources, adapt tasks, and review accuracy within an approved language or variety scope. Onboarding, source provenance, review history, and attribution should make that authority clear.

Specialists can also describe a proposed feature and use an explicitly invoked AI operation to organize it into an editable Markdown handoff. Specialist correction precedes architectural review; the founder approves development after reviewing the resulting design. Implementation and linguistic validation follow through their governed workflows. The specialist role alone does not confer legal rights approval, access to learner records, or authority to create LAOS evidence or state.

### Operations Health Center

The full OHC follows the Linguistic Specialist release. It should track provider usage, credits, failures and retries, per-user and total operating costs, and service health. Cost per useful completed task should include relevant speech and AI services. Budgets and reporting should support sustainable access without unnecessary exposure of learner content or behavioral surveillance; basic monitoring continues before the dashboard exists.

---

## Acceptance criteria for AI-supported features

Comparing other products, including Duolingo, should sharpen the relevant feature's acceptance criteria without creating a competing delivery sequence. These criteria apply when a later slice is commissioned; they do not expand current Media Center qualification or require learner pilots to finish before redesign migration.

- **Communicative purpose:** conversation and roleplay help a learner accomplish something, respond meaningfully, ask for clarification, and finish an exchange. Beginner supports are welcome and remain visible when interpreting performance.
- **Useful feedback:** explanations address the actual response and plausible intention, recognize valid alternatives, and support a purposeful second attempt. Reviewed reusable content is preferred where it meets the need reliably.
- **Trustworthy speech access:** check transcription, intelligibility feedback, correction and retry paths, device support, and accessible alternatives. A recognition error must not silently become a learner error.
- **Language and cultural quality:** specialists review relevant examples, varieties, and scenarios. Content keeps its provenance, permissions, version, and publication decision.
- **Meaning through visual design:** planned pronoun transformations and tense/mode visualizations connect speaker intention, linguistic choice, and interpretation. Motion is learner-controlled, with a static accessible equivalent; full new interactive lessons remain later pilot work.
- **Evidence and agency:** model output, task completion, personal interests, language choices, and time away do not automatically establish proficiency or motivation. Recommendations preserve teacher authority and learner choice.
- **Rights and privacy:** each source and proposed operation must pass its applicable checks. Playback access does not by itself authorize copying, translating, exporting, or sending source material and source-dependent responses to AI.
- **Operational usefulness:** set quality, latency, and cost thresholds before a pilot; measure failures, retries, and the cost of a useful completed task. Generation should contribute value that justifies its use.

Small pilots can identify usability problems and inform specialist and teacher judgment through fresh communicative tasks. Broader acquisition claims require an appropriate study; clicks, session length, or favorable surveys alone are insufficient. Human exchange beyond Concordance remains the purpose, and returning to the platform remains optional.

## Frameworks and validation

Framework projections remain interpretations of learner state rather than learner identity or official certification. ACTFL and CEFR are central to the current direction, and Interagency Language Roundtable (ILR) support is planned as a future governed framework projection. Concordance may also support preparation contexts such as AP and AAPPL without claiming affiliation with or certification authority from the organizations that maintain those systems.

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

The French-first platform already includes substantial work across placement, adaptive practice, conversation, pronunciation, listening, writing, authentic cultural contexts, teacher analytics, assessment-preparation contexts, privacy, and safety. The governed pipeline now extends through implemented and accepted Recommendation, Individual Learner Path, and bounded Experience Resolution runtimes for the currently supported Concordance-native practice path. Media Center source implementation now extends beyond retained media-revision identity and bounded database authority into rights-policy, eligibility, availability, deliverability, and resolution-authority foundations. Source presence does not establish completed qualification, deployment, or production activation. No external provider is admitted through the supported path, and public authentic-media resolution and preview remain closed.

For a current visual overview, see **[FEATURES.md](FEATURES.md)**. For dated engineering progress, see **[UPDATES.md](UPDATES.md)**.

---

## Support or participate

- **[Sponsor the project](https://github.com/sponsors/monsieur-trenton)** to help cover infrastructure and responsible development.
- **[Support Concordance on Patreon](https://www.patreon.com/Concordance_Learn)** for another way to sustain the work.
- **[Open an issue](https://github.com/monsieur-trenton/concordance/issues/new/choose)** with a pilot, research, partnership, or feature idea.
- Review **[CONTRIBUTING.md](CONTRIBUTING.md)** for ways to help while the production core remains private.
