<!-- Partenaire ($1,000/mo) sponsors get top-tier logo placement here. Add new sponsor logos above this line. -->

# Concordance

**An AI-driven French learning platform built on validated proficiency frameworks, not just vocabulary games.**

[![Sponsor](https://img.shields.io/badge/sponsor-monsieur--trenton-ea4aaa?logo=github-sponsors)](https://github.com/sponsors/monsieur-trenton)
[![Live Site](https://img.shields.io/badge/live%20site-concordancelearn.com-blue)](https://concordancelearn.com/)

**[→ Try Concordance at concordancelearn.com](https://concordancelearn.com/)**

---

## The problem

Most language-learning apps optimize for streaks and vocabulary drills. They rarely tell a student, a teacher, or a parent where a learner actually *stands* - not in points, but in real proficiency: can this student handle the syntax of a formal email, or analyze a literary passage with confidence?

Concordance is built the other way around. Every exercise, every AI-generated passage, and every progress metric is anchored to established proficiency frameworks (ACTFL, AAPPL, CEFR) - so growth is measured against real-world communicative competence, not just a streak counter. Every surface - student, teacher, admin - shares a consistent, deliberately designed interface, not a bolted-together set of screens.

## Guiding principles

### 1. AI does the grunt work, the teacher keeps the judgment
AI handles the labor - drafting practice content, surfacing class-wide misconception patterns, generating progress summaries - but a teacher reviews and approves what reaches students, and pedagogical judgment stays with the human expert. Concordance is built to amplify teachers, not replace them. Every AI feature is designed around keeping the educator in the loop.

### 2. Students can drive their own learning independently
While teachers have full oversight and curation capabilities, the platform functions as a fully autonomous tutor for the self-directed learner. Through the continuous diagnostic loop, adaptive practice, and the AI-driven "Focus Areas" dashboard, a student without a classroom can independently practice, identify their weaknesses, and systematically improve their proficiency.

## What it does

### For students
- **Intelligent Diagnostic Loop**: Bayesian Knowledge Tracing estimates the student's mastery of each grammar concept from every graded attempt across the platform - practice, writing, and conversation alike. When mastery of a concept stays low (e.g., subjunctive vs. indicative), the dashboard prescribes the exact grammar tutorial and targeted practice activity needed, closing the loop on remediation.
- **Adaptive practice** that targets a student's actual proficiency sublevel (Novice → Intermediate → Advanced), not just their grade level.
- **AI-generated content** - reading passages, grammar tutorials, vocabulary in context - calibrated to a specific ACTFL/AAPPL target and reviewed before reaching students.
- **Gamification** that stays in service of proficiency: points, streaks, and a score multiplier on daily review.
- **Cultural Adventure Hub** - interactive conversational scenarios - plus presentation recording for real speaking practice.
- **Proficiency-aware conversation partner** - an AI partner that holds a French conversation calibrated to the student's current ACTFL level (comprehensible input, *i+1*), tags the grammar errors it hears, and feeds them straight back into the diagnostic loop. Backed by a Google Gemini → Anthropic Claude fallback so a single provider's outage never takes the feature down.
- **Listening comprehension** - AI-generated French audio passages with comprehension questions, voiced by a choice of text-to-speech engines (ElevenLabs or Google Gemini) with automatic fallback for reliability.
- **AAPPL-aligned expression tools**: a scoring rubric, a practice simulator, and a targeted word bank for exam prep.
- **A portfolio** that showcases a student's growth over time, not just a grade snapshot.
- **A translanguaging hub** for advanced learners working with authentic literary and socio-cultural texts, gated by demonstrated proficiency rather than a calendar.
- **A [dedicated Framework page](https://concordancelearn.com/framework)** that explains the underlying socio-cognitive model in plain language, for students and parents who want to understand the *why*.
- **Weekly guardian digest** - an opt-in email that gives a parent or guardian a plain-language summary of the week: activity, proficiency, and the concepts the student is currently working on.
- A bilingual (French/English) interface.

### For teachers
- **Class and roster management** with proficiency heatmaps across a whole cohort, not just individual grades.
- **One-click class remediation** - Concordance surfaces the concepts a whole class is collectively struggling with (from the same error data that drives each student's diagnostic loop) and generates a targeted set of practice exercises for the cohort on demand.
- **AI-assisted content generation** for their own classes - reading passages, grammar tutorials, vocabulary, and listening passages (audio + questions) - that teachers curate and approve, not just consume.
- **Community Content Marketplace** to share and reuse content other teachers have built.
- **A feature-voting board** so teachers help shape what gets built next.
- **School and district profile fields** to keep reporting and content generation grounded in real classroom context.

### For admins
- **AI Studio**: generate and moderate content at scale - reading, grammar, vocabulary, and listening (with a choice of ElevenLabs or Google Gemini text-to-speech) - with an Anthropic Claude → Google Gemini fallback so generation stays reliable.
- **User management tools**, including CSV import/export for rosters.
- **Maintenance mode** for safe deploys without surprising users mid-session.
- **AI-moderated review** of community feature ideas before they go live.

## Privacy & compliance

Concordance is designed to be fully compliant with major educational privacy laws in the US and Europe:

- **FERPA (US)**: We protect student education records. Teachers and administrators have complete control over student data, including cascading deletion tools.
- **COPPA (US)**: Students under 13 cannot sign up independently. Concordance relies on the School Consent Exception when teachers add younger students to their classes. Student data is never used for advertising.
- **PPRA (US)**: We do not prompt students with surveys regarding protected or sensitive personal affiliations.
- **GDPR (EU)**: We require explicit consent for data processing (Terms of Service and Privacy Policy checkboxes). All users have a self-service "Delete Account" button to exercise their Right to be Forgotten. We practice data minimization.

## Why the core is closed

The proficiency-encoding engine - how exercises, AI prompts, and progress tracking are mapped to ACTFL/AAPPL/CEFR sublevels - is the result of a lot of iteration, and it's the part of Concordance that's genuinely novel. That logic lives in a private repository for now. This repo exists to show what the project is and where it's headed, and to make it easy to support the work directly. For now, potential sponsors can go to [https://www.concordancelearn.com/](https://www.concordancelearn.com/) to preview features or take the tour.

## What sponsorship funds next

The full, ordered list lives in **[ROADMAP.md](ROADMAP.md)** - including what's recently shipped, what's being built now, and the longer-term vision. The short version:

French is live today - and **Automated Speech Recognition (ASR)**, the **proficiency-aware conversation partner**, **listening comprehension**, and **one-click class remediation** described above all shipped recently, alongside an upgrade of the diagnostic engine to real Bayesian Knowledge Tracing. See **[ROADMAP.md](ROADMAP.md)** for the full recently-shipped list.

The next headline is **deeper teacher analytics**: surfacing early-warning flags and cohort trend lines on top of the new per-concept mastery model, going beyond one-click remediation.

This is built solo, nights and weekends. In the short term, sponsorship covers the zero-fun parts - server hosting and the AI / text-to-speech API bills that features like the conversation partner, ASR, and listening audio now incur - and buys the time to build the next thing properly instead of rushing it. **The ultimate goal is to transition to building these tools full-time**, ensuring long-term maintenance, faster feature delivery, and the ability to keep the core platform free for students and public schools.

**Further out:** Concordance's translanguaging hub already pulls from Francophonie beyond Metropolitan French (Senegalese and Maghrebi literature, for instance) - the plan is to build that out into a first-class feature rather than a handful of texts. And a **second language (Spanish)**, designed *aware* of French rather than as an isolated second product - modeling cross-linguistic transfer (cognates, false friends, structural interference) for learners building more than one language at once. That one waits until the right Spanish-speaking collaborator can do it justice; shipping it half-built would betray the proficiency-first premise.

## Support the project

If this resonates with you - as an educator, a fellow developer, or just someone who thinks language learning tools should be built on real pedagogy - consider [sponsoring on GitHub](https://github.com/sponsors/monsieur-trenton). Tier details and perks live on the [Sponsors page](https://github.com/sponsors/monsieur-trenton) and may evolve - that page is the source of truth, not this README.

Sponsors at the Ami tier and above are listed in [SPONSORS.md](SPONSORS.md).

## Project docs

- **[ROADMAP.md](ROADMAP.md)** - what sponsorship funds next.
- **[CONTRIBUTING.md](CONTRIBUTING.md)** - ways to get involved (the core is closed, but there's real work to share).
- **[SECURITY.md](SECURITY.md)** - responsible-disclosure policy and student-data posture.
- **[CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md)** - how we keep this an educator-friendly space.

## Contact

Questions, ideas, or interested in a deeper look under the hood? **[Open an issue](https://github.com/monsieur-trenton/concordance/issues/new/choose)** - there are templates for feature ideas and bug reports.
