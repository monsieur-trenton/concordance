# Security Policy

Concordance is an education platform used by students, teachers, and schools, so
security and student-data protection are taken seriously. Thank you for helping keep
learners safe.

## Scope

- This repository is the **public showcase** for Concordance. The running product lives
  at **[concordancelearn.com](https://concordancelearn.com/)**, and the proficiency engine
  is in a private repository.
- Reports about the **live application** (authentication, data exposure, access control,
  the AI Studio, etc.) are in scope and most valuable.
- Reports about **these public docs** (e.g. a broken or misleading link) are welcome too -
  a normal issue is fine for those.

## Reporting a vulnerability

**Please do not open a public issue for a security vulnerability.**

Use GitHub's private vulnerability reporting instead:

1. Go to the **[Security tab](https://github.com/monsieur-trenton/concordance/security)**
   of this repository.
2. Click **"Report a vulnerability"** to open a private advisory.

If you can, include: what you found, where (URL or endpoint), the impact you believe it
has, and the steps to reproduce. Proof-of-concept details shared privately are appreciated.

## What to expect

This is a solo project built nights and weekends, so responses are best-effort rather than
backed by an SLA. That said:

- You'll get an acknowledgement as soon as the report is seen.
- Confirmed issues affecting student data are treated as the top priority.
- Please give a reasonable window to ship a fix before any public disclosure, and avoid
  accessing or modifying data that isn't yours while testing.

## Student-data posture

Concordance is built to comply with **FERPA, COPPA, PPRA (US)** and **GDPR (EU)** - including
data minimization, self-service account deletion, and teacher/admin control over student
records. If you find anything that undermines that posture, it's exactly what this policy is
for.
