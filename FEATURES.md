# Concordance Features — Language, Culture, and Evidence

**[→ Explore Concordance](https://concordancelearn.com/)**

Concordance Learn is an evidence-informed language-learning platform for serious learners and teachers. Its purpose is to help people become more capable, confident, and independent participants in another language and culture—not simply to complete a course, accumulate points, or prepare for one examination.

French is the first implementation. The current product therefore contains substantial French-learning and teacher-support experiences, including preparation contexts such as AP French Language and Culture and AAPPL. Those contexts are important applications of Concordance, but they do not define the platform's larger purpose.

## Product at a glance

The public landing page introduces Concordance's visual language and its emphasis on communication, culture, and French as a living language.

![Concordance landing page](assets/screenshots/concordance-landing.png)

The anonymous public tours provide a safe overview of student and teacher experiences. They do not contain school data or authenticated learner information. All screenshots in this repository are public product views or synthetic demonstrations, not evidence of a particular learner's performance.

## Getting a demo link

Student-facing features are best explored through a **2-hour read-only demo session**. The demo requires no signup and saves no data. **[Request a demo link](https://concordancelearn.com/request-demo)** to receive a single-use link by email. See **[DEMO.md](DEMO.md)** for the walkthrough.

The demo shows selected product behavior, not private learner records or the complete future architecture. Some first-login experiences, including Point de départ, may be skipped or pre-calibrated.

## For learners

### A purposeful next step

The student experience is organized around manageable, meaningful work rather than an endless activity catalog. The home experience can bring together due review, focus areas, current proficiency context, and opportunities to practice.

The implemented Individual Learner Path foundation extends this direction at the backend by turning governed educational intelligence into individual learning opportunities. It preserves the distinction between advisory recommendations, teacher-required work, and learner choice. This does not mean that every contemplated ILP experience is already available in the public interface.

### Point de départ and evidence-informed practice

Point de départ gives a new learner a short adaptive check-in before regular practice. It establishes a more useful starting position than an assumed zero or unsupported self-rating.

Across graded work, Concordance can identify patterns so practice responds to demonstrated needs. Learners can encounter explanations and focus areas connected to:

- verb conjugation and tense relationships;
- agreement, connectors, and pronoun use;
- vocabulary in context and false friends;
- reading and listening comprehension; and
- communication choices that affect meaning, register, and clarity.

The system offers estimates and guidance, not infallible judgments. Missing evidence is not evidence of inability, and teacher review remains important where a classroom context is available.

### Practice that serves communication

Available practice includes:

- **Conjugaison** — verb conjugation with grammatical feedback;
- **Vocabulaire** — vocabulary in context with spaced review;
- **Compréhension** — reading and listening with contextual support;
- **Connecteurs** — logical connectors and discourse markers;
- **Pronoms** — relative pronouns and object complements;
- **Concordance des temps** — sequence of tenses and subjunctive conditioning;
- **Sentence Builder** — sentence construction with distractors and explanations; and
- additional grammar and communication activities.

Grammar and vocabulary are not the final objective. They are resources for understanding people, expressing meaning, and participating more effectively in the language.

### Speaking, listening, and pronunciation

Learners can work through:

- recorded speaking responses;
- conversation practice;
- pronunciation feedback;
- phoneme-level work through the IPA Pronunciation Wizard; and
- graded speaking tasks using conversation transcripts where available.

Speaking evidence remains distinct from transcription, provider output, and proficiency interpretation. Feedback is intended to support intelligibility, confidence, and communication—not to reduce a learner to an automated score.

### Writing and learner goals

Writing tools support general French development as well as particular academic or assessment goals. Learners can work with:

- generalized writing diagnostics;
- interactive proofreading and error explanations;
- presentational and interpersonal writing;
- organization, supporting evidence, register, and communicative effectiveness;
- AAPPL-oriented expression tasks; and
- AP-style prompts using text, infographic, and audio stimuli.

AP and AAPPL tasks are preparation contexts within a broader communicative program. Concordance is not an examination board or certification authority.

### Authentic language and culture

The Cultural Adventure Hub and related experiences place French in recognizable social and cultural contexts across multiple Francophone regions. The Translanguaging Hub supports advanced work with literary, regional, historical, and cultural material.

The longer-term Media Center direction treats texts, audio, video, music, journalism, literature, visual art, photography, and other cultural artifacts as first-class resources. Authentic culture should not be reserved for advanced learners: educational demand belongs to the task and scaffold, not to an intrinsic level assigned to the resource.

The accepted Media Center architecture and first retained-identity implementation slice establish part of the foundation for this work. No external provider is currently admitted through that slice, and public authentic-media delivery remains closed pending later rights, selection, availability, deliverability, and learner-scaffolding work.

### Portfolio and bilingual access

The portfolio brings together selected work and progress over time rather than presenting one score as the whole learner record. The interface supports French and English so learners can access the platform while continuing to work toward meaningful French participation.

## For teachers

### Teacher Hub and Learner Evidence Profiles

The teacher experience is intended to make learner development understandable from evidence rather than from an opaque total score. Teacher views can include:

- class and learner progress patterns;
- instructional targets and estimated proficiency context;
- concept-level strengths, gaps, and uncertainty;
- provenance for work supporting a conclusion; and
- teacher observations, support, and contestation workflows.

Teachers retain professional judgment. Evidence informs what Concordance understands; teachers determine how that understanding should shape instruction in their context.

### Class patterns and targeted support

Teachers can inspect class-level patterns and individual learner needs. Heatmaps, concept summaries, and trend views can help identify where a group may benefit from shared instruction and where a learner may need different support.

When several learners show a similar need, teachers can create or select targeted practice. AI-assisted tools can help draft passages, explanations, vocabulary sets, and listening activities, but generated content remains subject to human review. A model does not become an educational authority merely because it can produce material quickly.

### Classroom context

Teacher and school workflows can support roster management, assignments, classroom priorities, and preparation contexts. These workflows augment educators; they do not replace teaching, relationships, or professional judgment.

## The governed educational foundation

Concordance's protected reasoning architecture, LAOS, separates stages that many systems collapse into one opaque score:

```text
Learner performance
→ evidence assertions
→ learner state
→ framework interpretation
→ recommendations
→ individual learning opportunities
→ bounded experience resolution
```

The Recommendation Engine and Individual Learner Path runtimes are implemented and accepted through their defined backend boundaries. Downstream Experience Resolution is implemented and accepted for the currently supported Concordance-native practice path. These are architectural and backend milestones, not claims that every planned learner-facing experience or delivery modality is complete.

The separation matters because:

- an error is not automatically a deficiency;
- completion is not automatically mastery;
- lack of evidence is not evidence of inability;
- a framework interpretation is not the learner's identity;
- a recommendation is not a command; and
- learner choices do not silently become proficiency or motivation judgments.

## Current product, implemented foundations, and future work

### Available product experiences

Subject to role and rollout availability, the current product includes practice, conversation, speaking, writing, listening, cultural scenarios, portfolio views, teacher analytics, content-support workflows, and administration.

### Implemented backend foundations

- deterministic learner-state and framework-projection foundations;
- Recommendation Engine runtime;
- Individual Learner Path runtime and governed opportunity access;
- bounded Experience Resolution for the supported Concordance-native practice path; and
- the first Media Center retained-identity and database-authority slice.

Implementation does not imply universal public availability, complete pedagogical validation, official framework certification, or support for every contemplated delivery path.

### In development or future direction

- learner-facing expansion of governed individual opportunities;
- later Media Center rights, provider-admission, selection, availability, deliverability, and scaffolding layers;
- richer teacher-facing evidence and guidance experiences;
- Language Specialist Workspace capabilities;
- governed research and validation infrastructure;
- future ILR framework projection work; and
- additional languages developed with appropriate linguistic, pedagogical, and community expertise.

These directions are not promises of dates, partnerships, official endorsement, or complete framework coverage.

## Frameworks and independence

Concordance uses ACTFL, CEFR, AP, AAPPL, and related terminology to explain interpretation and preparation contexts. Frameworks are perspectives and reference points, not learner identities or substitutes for the underlying evidence.

Concordance is an independent project and is not affiliated with, sponsored by, or endorsed by ACTFL, Language Testing International, the Council of Europe, the College Board, or other assessment organizations. Only authorized organizations and qualified professionals can determine official assessment or certification status.

## Privacy and responsible operation

Concordance is designed around educational privacy, data minimization, human review, learner isolation, and appropriate control over student records. Student data is not used for advertising, and ordinary activity should not silently become evidence merely because it can be collected.

Administrative and operational safeguards support the product, but they are not the educational story. Concordance exists to help people participate more meaningfully in languages and cultures and to help teachers understand development without surrendering judgment.

See the **[Security & Privacy Policy](SECURITY.md)** for the published data-protection posture.

## Recommended exploration path

For a two-hour demo, explore:

1. **Practice** — try several activity types and read the explanations;
2. **Conversation Partner** — have a short French dialogue;
3. **Cultural Scenarios** — explore a Francophone context;
4. **Speaking or Writing** — submit a short response and review feedback;
5. **Focus Areas** — inspect how recurring needs are presented; and
6. **Portfolio** — review progress over time.

## Support the project

Concordance is built and maintained by a practicing teacher. Sponsorship helps cover infrastructure, carefully controlled AI usage, accessibility, privacy, cultural and language expertise, and the time required to move from sound foundations to dependable learner-facing experiences.

**[Sponsor Concordance on GitHub →](https://github.com/sponsors/monsieur-trenton)**

See **[ROADMAP.md](ROADMAP.md)** for current priorities and what sponsorship funds next.
