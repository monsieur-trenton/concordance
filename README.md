<!-- Partenaire ($1,000/mo) sponsors get top-tier logo placement here. Add new sponsor logos above this line. -->

# Concordance

**The proficiency operating system for serious language learners and teachers.**

[![Sponsor](https://img.shields.io/badge/sponsor-monsieur--trenton-ea4aaa?logo=github-sponsors)](https://github.com/sponsors/monsieur-trenton)
[![Live Site](https://img.shields.io/badge/live%20site-concordancelearn.com-blue)](https://concordancelearn.com/)

**[→ Try Concordance](https://concordancelearn.com/)**  
**[→ Explore the features](FEATURES.md)**  
**[→ See what sponsorship funds](ROADMAP.md)**

---

## Why Concordance exists

I teach AP French, and I have watched students spend years studying a language without receiving a clear, defensible picture of what they can actually do with it.

Most language-learning software measures completion, points, or streaks. Concordance is built around a different question:

> **What evidence shows that this learner is becoming more capable in the language, and what should happen next?**

Concordance helps learners prepare for **AP French Language and Culture**, **AAPPL**, and **State Seals of Biliteracy** through practice tied to real communicative proficiency. It helps teachers see the evidence behind a learner's progress instead of asking them to trust an opaque score or an unexplained AI recommendation.

The goal is not to maximize time spent in an app. The goal is to help learners participate more meaningfully in French.

## Concordance and LAOS

Concordance is the first application of **LAOS — the Language Acquisition Operating System**.

LAOS is the evidence-centered reasoning architecture beneath the platform. It separates observed learner performance from proficiency interpretation, framework mapping, and recommendations. That makes it possible to preserve provenance, explain why guidance was produced, and improve the system without tying it permanently to one AI provider or one assessment framework.

In practical terms, the system is designed around a one-way evidence pipeline:

```text
Observation → Evidence → Learner State → Framework Projection → Recommendation
```

French is the first implementation. The longer-term vision is a reusable foundation for evidence-based language acquisition across languages, programs, and assessment contexts.

### A significant architectural milestone

The foundation now has an accepted architectural path from learner performance to future instructional guidance: evidence can inform a canonical learner model, that model can be interpreted through independent proficiency frameworks, and a governed recommendation layer can identify valuable next opportunities. The design emphasizes provenance, replayability, and an inspectable rationale rather than an opaque score or an AI-generated path that cannot be challenged.

This is an architecture milestone, not a claim that the Recommendation Engine is already running in the product. Its runtime implementation is the next engineering phase. The learner-facing work that will turn educational intelligence into individualized opportunities, including the future Individual Learner Path, remains ahead.

Concordance is therefore not fundamentally a chatbot that teaches languages or a system that decides exactly what every learner must do next. It is being built as educational infrastructure that helps learners and teachers understand demonstrated communication over time. AI can assist within that system; it is not the educational authority. Learners retain choice and pace, while teachers can guide priorities, inspect evidence, and redirect instruction.

## What sponsors are supporting

Concordance is built and maintained by a practicing teacher outside the school day. I intend to keep teaching: the classroom is where the project remains accountable to real learners and real instructional needs.

GitHub sponsorship is the most direct way to keep the platform available while it is still early. Sponsorship currently helps cover:

- hosting, databases, storage, email, and monitoring;
- speech recognition and text-to-speech services;
- carefully limited AI inference for feedback, conversation, and content support;
- accessibility, security, and privacy work;
- continued development of evidence-driven AP French and AAPPL preparation;
- the transition from accepted educational-intelligence architecture into Recommendation Engine implementation and individualized learner experiences;
- small classroom pilots and future research collaboration.

The immediate objective is simple:

> **Keep the services and APIs running without placing the cost on students or individual public-school teachers.**

As LAOS matures, more reasoning can be handled through deterministic evidence logic, caching, and precomputed learner state rather than repeated model calls. This is both a technical priority and an affordability commitment.

## What Concordance does

### For learners

- Evidence-informed proficiency guidance aligned with ACTFL and CEFR.
- AP French and AAPPL-oriented speaking, writing, listening, and interpretive practice.
- Point de Départ placement and proficiency-aware daily practice.
- A conversation partner calibrated to the learner's current proficiency.
- Writing and speaking feedback connected to the learner's developing evidence profile.
- Grammar, thematic vocabulary, and communication recommendations grounded in observed needs.
- A portfolio that emphasizes growth over time rather than a single score.

### For teachers

- A Learner Evidence Profile that separates instructional targets from estimated proficiency.
- Transparent evidence strength, provenance, and contestation workflows.
- Class-level patterns and targeted remediation without surrendering professional judgment.
- Teacher-reviewed content generation and proficiency-aligned practice support.
- AP French and AAPPL preparation grounded in communicative performance rather than drill completion alone.

A fuller visual overview is available in **[FEATURES.md](FEATURES.md)**.

## Why the LAOS core is protected

The public Concordance repository documents the mission, product direction, sponsorship model, and ways to participate. The LAOS reasoning kernel and the production application code remain private for now.

That choice protects the part of the project that represents years of pedagogical judgment and architectural development: evidence modeling, learner-state computation, confidence and contestation rules, framework projections, and recommendation logic.

This does **not** prevent Concordance from contributing to research or public educational work. Research findings, evaluation methods, selected schemas, documentation, and appropriate shared infrastructure can be published openly while pre-existing LAOS intellectual property remains under its owner's stewardship.

It is easier to open more of the system later than to recover ownership of code released too early.

## Principles

1. **Evidence before recommendation.** Guidance should be traceable to observed learner performance.
2. **Teachers retain judgment.** AI can reduce labor, but it should not silently replace professional decisions.
3. **Frameworks are projections, not identities.** ACTFL, CEFR, AP, and AAPPL mappings should derive from evidence rather than become the learner record itself.
4. **Gamification serves pedagogy.** Engagement mechanics are tools, not product objectives.
5. **Cost discipline is part of access.** The platform should call external AI services only when they add meaningful pedagogical value.
6. **Learner attention is earned.** Concordance should maximize meaningful participation in the target language, not dependence on the platform.

The same boundaries guide future recommendations: evidence informs what the system understands, while learner goals, learner choices, teacher guidance, and educational context shape what happens next. Individualized learning is not intended to mean a compulsory, machine-predicted route.

## Privacy and independence

Concordance is designed around educational privacy, data minimization, human review, and learner isolation. Student data is not used for advertising.

Concordance describes its work using ACTFL, AAPPL, CEFR, and AP® terminology so educators and learners understand the preparation context. It is an independent project and is not affiliated with, sponsored by, or endorsed by ACTFL, Language Testing International, the Council of Europe, or the College Board. All marks belong to their respective owners.

## Support the project

If you believe language-learning technology should be transparent, evidence-centered, teacher-informed, and affordable, please consider **[sponsoring Concordance on GitHub](https://github.com/sponsors/monsieur-trenton)**.

Tier details and perks live on the Sponsors page, which remains the source of truth. Sponsors at the Ami tier and above are recognized in **[SPONSORS.md](SPONSORS.md)**.

## Project documents

- **[FEATURES.md](FEATURES.md)** — visual product overview.
- **[ROADMAP.md](ROADMAP.md)** — current priorities and what sponsorship funds.
- **[UPDATES.md](UPDATES.md)** — dated development updates.
- **[CONTRIBUTING.md](CONTRIBUTING.md)** — ways to help while the production core remains private.
- **[SECURITY.md](SECURITY.md)** — responsible disclosure and student-data posture.
- **[CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md)** — community expectations.

## Contact

Questions, research interest, pilot ideas, or partnership proposals are welcome through **[GitHub Issues](https://github.com/monsieur-trenton/concordance/issues/new/choose)**.
