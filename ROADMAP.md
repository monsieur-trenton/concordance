# Roadmap

This roadmap is the honest version of "what your sponsorship funds." Concordance is
built solo, nights and weekends, so the list below is ordered by intent, not by a
promised delivery date. Sponsorship doesn't buy a feature on a calendar - it buys the
time to build the next thing properly instead of rushing it, and it keeps the core
platform free for students and public schools.

Tier details and perks live on the **[Sponsors page](https://github.com/sponsors/monsieur-trenton)**,
which is the source of truth for what each tier includes.

---

## Where your money goes

Concordance is free for students and public schools, but the AI features aren't free
to run. Every conversation-partner reply, every ASR pronunciation check, and every
listening passage costs real money in API and hosting bills. Sponsorship covers those
bills so the platform can stay free for the people it's built for.

<!-- TODO (owner): replace the bracketed figures below with real monthly numbers from
     your provider dashboards (Anthropic, Gemini, Deepgram, ElevenLabs, Resend, AWS S3,
     Railway). Do not ship placeholder numbers publicly. -->

Roughly where a month of running costs goes today:

- **AI generation (Claude / Gemini):** ~$[XX]/mo — reading passages, grammar tutorials,
  the conversation partner, content moderation.
- **Speech (Deepgram ASR + TTS):** ~$[XX]/mo — pronunciation feedback and listening audio.
- **Hosting + database (Railway) and storage (S3):** ~$[XX]/mo.
- **Email (Resend):** ~$[X]/mo — password resets, reminders, weekly guardian digests.

**Total: ~$[XXX]/month**, today, at the current number of active students.

### Sponsor a classroom

A useful way to think about a sponsorship: roughly **$[XX]/month covers the AI costs of
one classroom** for a month — a teacher and their students using the conversation partner,
listening practice, and adaptive content, free of charge. Tier names and exact perks live
on the **[Sponsors page](https://github.com/sponsors/monsieur-trenton)**, which is the
source of truth.

---

## Recently shipped

These are live today on [concordancelearn.com](https://concordancelearn.com/):

- **Proficiency-aware conversation partner** - an AI partner that converses in French at
  the student's current ACTFL proficiency level (comprehensible input, *i+1*), tags the grammar errors
  it hears, and feeds them into the diagnostic loop. A Google Gemini → Anthropic Claude
  (Haiku) fallback keeps it running when a provider isn't.
- **Listening comprehension** - AI-generated French audio passages with comprehension
  questions, voiced by a choice of text-to-speech engines ([ElevenLabs](https://try.elevenlabs.io/ckt4iyz3r94t) or Google Gemini)
  with automatic fallback.
- **One-click class remediation** - surfaces the concepts a whole class is collectively
  missing and generates a targeted practice set for the cohort on demand.
- **Real Bayesian Knowledge Tracing** - the diagnostic engine now estimates per-concept
  mastery from every graded attempt (practice, writing, and conversation), replacing the
  earlier heuristic, and prescribes the exact tutorial and practice to close the gap.
- **Weekly guardian digest** - an opt-in email summarizing a student's week (activity,
  proficiency, current focus areas) for a parent or guardian.
- **Professional Learning modules** - teacher PD content with admin authoring tools.
- **Peer Hubs** - collaborative spaces for students.
- **Community Content Marketplace** - teachers share and reuse content they've built.
- **AI Studio** with an Anthropic Claude → Google Gemini fallback so generation stays
  reliable, plus per-teacher and global opt-in controls.
- **Expression tools to prepare for the AAPPL exam** - scoring rubric, practice simulator, targeted word bank.
- **Automated Speech Recognition (ASR).** Real-time feedback on spoken production, built
  on top of the conversation partner - so the platform coaches pronunciation and fluency,
  not just written accuracy. Includes per-user admin beta toggles.
- **Deeper teacher analytics** - concept-mastery trend lines per class, early-warning flags
  (plateau, slide, outlier, stalled) computed by a daily risk-assessment job, and per-skill
  cohort summaries - going beyond one-click remediation.
- **Graded speaking tasks** - students submit conversation-partner transcripts for AI
  scoring (fluency, accuracy, complexity) via Claude with a Gemini fallback, and the scores
  feed straight into the same per-concept mastery model as every other graded attempt.
- **AP-exam essay practice.** A dedicated essay tool with text/infographic/audio stimulus
  sets aligned to official AP French themes, AI-generated feedback, and a submission
  history - so students can rehearse the AP-style integrated essay format directly on the
  platform.
- **A generalized writing diagnostic.** Any piece of French writing - not just essays - can
  now get AI feedback calibrated to the student's ACTFL sublevel (Novice High through
  Advanced), each with its own proficiency-appropriate accuracy, complexity, and fluency
  bar.
- **Interactive proofreading with morphosyntactic error analysis.** Submitted writing is
  now annotated with character-anchored error tags (verb conjugation, tense sequencing, and
  more) for an inline proofread view, and every tagged error feeds the same diagnostic loop
  that drives practice and remediation everywhere else.

## Now - building

- **Expanded listening & cultural content** - grow the generated audio library across more
  Francophone contexts, registers, and proficiency levels. Includes AI-generated interpretive questions.

## Later - vision

- **Francophonie as a first-class feature.** The translanguaging hub already pulls from
  beyond Metropolitan French (Senegalese and Maghrebi literature, for instance). The plan
  is to build that breadth out properly rather than ship a handful of texts.
- **A second language (Spanish), done right.** Extend the proficiency-first design to
  Spanish - and design it *aware* of French (cognates, false friends, structural
  interference) rather than as an isolated product. This one waits until the right
  Spanish-speaking collaborator can do it justice; shipping it half-built would betray
  the whole premise.
- **Full-time development.** The ultimate goal is to move from nights-and-weekends to
  building these tools full-time, which is what makes long-term maintenance and faster
  delivery realistic.

---

Have an idea or a priority you'd weigh differently? **[Open an issue](https://github.com/monsieur-trenton/concordance/issues/new/choose)** -
sponsor input genuinely shapes this list.
