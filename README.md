<!-- Sponsor recognition may appear here when the current sponsorship program supports it. -->

# Concordance

**The proficiency operating system for serious language learners and teachers.**

**Language · Culture · Connection**

[![Sponsor](https://img.shields.io/badge/sponsor-monsieur--trenton-ea4aaa?logo=github-sponsors)](https://github.com/sponsors/monsieur-trenton)
[![Live Site](https://img.shields.io/badge/live%20site-concordancelearn.com-blue)](https://concordancelearn.com/)

**[→ Try Concordance](https://concordancelearn.com/)**  
**[→ Why Concordance?](WHY_CONCORDANCE.md)**  
**[→ Explore the features](FEATURES.md)**  
**[→ See what sponsorship funds](ROADMAP.md)**

---

## Why Concordance exists

I teach French, including AP French, and I have watched learners spend years studying a language without receiving a clear, defensible picture of what they can actually do with it—or enough meaningful contact with the people, cultures, and ideas that make the language worth learning.

Most language-learning software measures completion, points, or streaks. Concordance is built around a different question:

> **What evidence shows that this learner is becoming more capable in the language, and what should happen next?**

Concordance Learn is an evidence-informed language-learning platform being built to help people become more capable, confident, and independent participants in another language and culture. It connects meaningful communication, authentic cultural material, understandable evidence, and carefully governed guidance while preserving learner choice and teacher judgment.

French is the first implementation. The current platform can support goals such as AP French Language and Culture, AAPPL, and State Seals of Biliteracy, but assessment preparation is one application of the work—not the identity of the platform. Learners may also be studying for travel, professional or academic life, heritage connection, literature and media, relationships, or the lasting value of learning another language.

The goal is not to maximize time spent in an app or progress through a proprietary course. The goal is meaningful participation beyond the platform.

## Concordance and LAOS

Concordance is the first application of **LAOS — the Learner Analysis and Orchestration System**.

LAOS is the evidence-centered reasoning architecture beneath the platform. It provides a governed way to preserve and connect learner observations, evidence assertions, learner-state information, framework projections, recommendations, and individual learning opportunities without collapsing them into one opaque score. It separates observed learner performance from proficiency interpretation, framework mapping, recommendations, and downstream learning experiences. That makes it possible to preserve provenance, explain why guidance was produced, and improve the system without tying it permanently to one AI provider or one assessment framework. LAOS is not an automated certification authority: teachers retain professional judgment, learners retain agency and pace, and framework labels remain read-only projections rather than official credentials.

In practical terms, the system is designed around a one-way evidence pipeline:

```text
Evidence → Evidence Assertions → Learner State → Framework Projection → Recommendation → Individual Learning Opportunities
```

French is the first implementation. The longer-term vision is a reusable foundation for evidence-based language acquisition across languages, programs, and assessment contexts.

### From the Individual Learner Path to governed experiences

The Individual Learner Path, or ILP, is now implemented and accepted through its intended architectural boundary. It can turn governed upstream educational intelligence into individual learning opportunities while preserving where those opportunities came from. A recommendation remains a recommendation, teacher-required work remains authoritative, and learner choice remains distinct from both. The ILP does not predict how quickly someone should learn or turn individualized learning into a compulsory machine-generated curriculum.

The downstream Experience Resolution boundary is also implemented and accepted for the currently supported Concordance-native practice path. It resolves a verified opportunity into a specific, revision-bound experience without allowing delivery mechanics to rewrite the educational decision. This remains an architectural and backend milestone, not a claim that every contemplated activity or learner-facing experience is finished.

The Media Center now has an accepted, rights-aware architecture, and its first implementation slice has established retained media-revision identity custody and bounded database authority. This foundation does not admit providers, select resources, assign proficiency levels, or activate public authentic-media delivery. Later work will govern source and rights evidence, current availability, deliverability, selection, and learner-facing scaffolding while keeping proficiency judgment attached to tasks and experiences rather than to cultural resources themselves. Concordance is not fundamentally a chatbot that teaches languages or a system that decides exactly what every learner must do next. It is being built as educational infrastructure that helps learners and teachers understand demonstrated communication over time and act on that understanding responsibly.

## What sponsors are supporting

Concordance is built and maintained by a practicing teacher outside the school day. The classroom keeps the project accountable to real learners and real instructional needs, while sponsorship can create the professional time needed for Concordance to grow without requiring that development time to come indefinitely from evenings and weekends with family.

The [GitHub Sponsors page](https://github.com/sponsors/monsieur-trenton) is now live. Sponsorship is the most direct way to keep the platform available while it is still early and currently helps cover:

- hosting, databases, storage, email, and monitoring;
- speech recognition and text-to-speech services;
- carefully limited AI inference for feedback, conversation, and content support;
- accessibility, security, and privacy work;
- continued development of evidence-informed language learning, beginning with French and including current AP French and AAPPL preparation contexts;
- the transition from educational intelligence and individual learning opportunities into useful learner experiences;
- small classroom pilots and future research collaboration.

The immediate objective is simple:

> **Keep the services and APIs running without placing the cost on students or individual public-school teachers.**

As LAOS matures, more reasoning can be handled through deterministic evidence logic, caching, and precomputed learner state rather than repeated model calls. This is both a technical priority and an affordability commitment.

## What Concordance does

### For learners

- Meaningful French communication across interpretive, interpersonal, and presentational modes.
- Authentic Francophone language and culture approached through appropriately designed tasks and scaffolds.
- Evidence-informed guidance that treats ACTFL and CEFR as useful perspectives rather than proprietary levels or official credentials.
- Speaking, writing, listening, reading, pronunciation, grammar, and vocabulary experiences connected to communicative goals.
- Point de Départ placement and practice responsive to demonstrated needs.
- A conversation partner and feedback tools designed to support communication rather than replace human relationships or judgment.
- A portfolio that emphasizes growth and evidence over time rather than a single score.
- Preparation contexts including AP French and AAPPL without reducing language learning to examination performance.

### For teachers

- A Learner Evidence Profile that separates observations, instructional targets, and framework interpretations.
- Transparent evidence strength, provenance, uncertainty, and contestation workflows.
- Class-level patterns and targeted support without surrendering professional judgment.
- Teacher-reviewed content generation and proficiency-aligned practice support.
- Support for communicative instruction and assessment preparation as parts of a broader language-and-culture program.

A fuller visual overview is available in **[FEATURES.md](FEATURES.md)**.

## Why the LAOS core is protected

The public Concordance repository documents the mission, product direction, sponsorship model, and ways to participate. The LAOS reasoning kernel and the production application code remain private for now.

That choice protects the part of the project that represents years of pedagogical judgment and architectural development: evidence modeling, learner-state computation, confidence and contestation rules, framework projections, recommendation logic, and individualized opportunity orchestration.

This does **not** prevent Concordance from contributing to research or public educational work. Research findings, evaluation methods, selected schemas, documentation, and appropriate shared infrastructure can be published openly while pre-existing LAOS intellectual property remains under its owner's stewardship.

It is easier to open more of the system later than to recover ownership of code released too early.

## Principles

1. **Evidence before recommendation.** Guidance should be traceable to observed learner performance.
2. **Teachers retain judgment.** AI can reduce labor, but it should not silently replace professional decisions.
3. **Frameworks are projections, not identities.** ACTFL, CEFR, AP, and AAPPL mappings should derive from evidence rather than become the learner record itself.
4. **Learners own their pace.** Individualization should create meaningful opportunities, not predict how quickly someone ought to progress.
5. **Gamification serves pedagogy.** Engagement mechanics are tools, not product objectives.
6. **Cost discipline is part of access.** The platform should call external AI services only when they add meaningful pedagogical value.
7. **Learner attention is earned.** Concordance should maximize meaningful participation in the target language, not dependence on the platform.

The same boundaries guide future recommendations: evidence informs what the system understands, while learner goals, learner choices, teacher guidance, and educational context shape what happens next. Individualized learning is not intended to mean a compulsory, machine-predicted route.

## Privacy and independence

Concordance is designed around educational privacy, data minimization, human review, and learner isolation. Student data is not used for advertising. The fact that Concordance possesses information does not mean every part of Concordance is entitled to use it, and ordinary learner behavior should not quietly become evidence about motivation or proficiency.

Concordance describes its work using ACTFL, AAPPL, CEFR, and AP® terminology so educators and learners understand the preparation context. It is an independent project and is not affiliated with, sponsored by, or endorsed by ACTFL, Language Testing International, the Council of Europe, or the College Board. All marks belong to their respective owners.

## Support the project

If you believe language-learning technology should be transparent, evidence-centered, teacher-informed, and affordable, please consider **[sponsoring Concordance on GitHub](https://github.com/sponsors/monsieur-trenton)**.

Current sponsorship availability, tiers, and benefits belong on the **[GitHub Sponsors page](https://github.com/sponsors/monsieur-trenton)**. Public sponsor recognition, when available, is described in **[SPONSORS.md](SPONSORS.md)**.

## Project documents

- **[WHY_CONCORDANCE.md](WHY_CONCORDANCE.md)** — the case for an evidence-informed, learner-centered approach to language learning.
- **[FEATURES.md](FEATURES.md)** — visual product overview.
- **[ROADMAP.md](ROADMAP.md)** — current priorities and what sponsorship funds.
- **[UPDATES.md](UPDATES.md)** — dated development updates.
- **[CONTRIBUTING.md](CONTRIBUTING.md)** — ways to help while the production core remains private.
- **[SECURITY.md](SECURITY.md)** — responsible disclosure and student-data posture.
- **[CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md)** — community expectations.

## Contact

Questions, research interest, pilot ideas, or partnership proposals are welcome through **[GitHub Issues](https://github.com/monsieur-trenton/concordance/issues/new/choose)**.
