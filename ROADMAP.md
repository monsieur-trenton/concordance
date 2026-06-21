# Roadmap

This roadmap is the honest version of "what your sponsorship funds." Concordance is
built solo, nights and weekends, so the list below is ordered by intent, not by a
promised delivery date. Sponsorship doesn't buy a feature on a calendar - it buys the
time to build the next thing properly instead of rushing it, and it keeps the core
platform free for students and public schools.

Tier details and perks live on the **[Sponsors page](https://github.com/sponsors/monsieur-trenton)**,
which is the source of truth for what each tier includes.

---

## Recently shipped

These are live today on [concordancelearn.com](https://concordancelearn.com/):

- **Proficiency-aware conversation partner** - an AI partner that converses in French at
  the student's current ACTFL level (comprehensible input, *i+1*), tags the grammar errors
  it hears, and feeds them into the diagnostic loop. A Google Gemini → Anthropic Claude
  (Haiku) fallback keeps it running when a provider isn't.
- **Listening comprehension** - AI-generated French audio passages with comprehension
  questions, voiced by a choice of text-to-speech engines (ElevenLabs or Google Gemini)
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
- **AAPPL-aligned expression tools** - scoring rubric, practice simulator, targeted word bank.
- **Automated Speech Recognition (ASR).** Real-time feedback on spoken production, built
  on top of the conversation partner - so the platform coaches pronunciation and fluency,
  not just written accuracy. Includes per-user admin beta toggles.

## Now - building

- **Deeper teacher analytics** - early-warning flags and cohort trend lines on top of the
  new per-concept mastery model, going beyond one-click remediation.

## Next

- **Expanded listening & cultural content** - grow the generated audio library across more
  Francophone contexts, registers, and proficiency levels. Includes AI-generated interpretive questions.
- **Graded speaking tasks** - combine the conversation partner with ASR scoring into
  speaking assessments that feed the same proficiency model.

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
