---
name: "app-compliance-audit"
description: "Code-level audit of an app for shipping compliance gaps (age gate, consent, tracking, email, subscriptions, uploads, PII). Use instead of legal:compliance-check when auditing a codebase before launch."
---

# App compliance audit

Flag gaps, don't give legal advice. Never claim the app is "compliant" — only report what was found in code.

## Step 1 — Scope
- If args name a feature/path, audit that. Otherwise audit the whole repo.
- Identify target markets (UAE, EU, US/California, other) from README, i18n, currency, CLAUDE.md. If unclear, ask once; if no answer, assume UAE + EU + US and say so.

## Step 2 — Detect what applies
Grep the codebase for signals before judging. Mark an item N/A only with evidence it doesn't apply.
- Signup/auth: register, signup, createUser, auth providers
- Tracking: gtag, analytics, posthog, mixpanel, hotjar, clarity, fullstory, logrocket, sentry replay, segment, meta pixel
- Third-party assets: fonts.googleapis.com, fonts.gstatic.com, external CDNs
- Email: resend, sendgrid, postmark, mailgun, ses, nodemailer
- Payments: stripe, paddle, lemonsqueezy, revenuecat, in-app purchase
- Uploads: file inputs, multer, S3/R2/storage buckets, image upload
- PII in logs: console.log / logger calls with email, password, token, phone

## Step 3 — Checklist
1. **Age gate** — signup asks age/DOB; under-13 blocked (COPPA), under-16 EU needs parental consent.
2. **Third-party leaks** — fonts self-hosted; no third-party scripts load before consent.
3. **Analytics / session replay** — off until consent; replay masks all inputs; password fields never recorded; consent banner exists if any tracking.
4. **Email** — marketing emails have one-click unsubscribe, sender physical address, honest subject; transactional separated.
5. **Subscriptions** — before pay button: price, interval, auto-renew notice, how to cancel; cancel as easy as signup; receipt + renewal reminder.
6. **User uploads** — ToS, copyright/takedown policy, report button; note DMCA agent registration if US users.
7. **Privacy docs** — Privacy Policy + Terms exist and match actual data collected; data deletion path exists; note UAE PDPL / GDPR / CCPA applicability.
8. **Secrets & PII** — no secrets in client bundle or repo; no PII in logs.

## Step 4 — Output
A table: `# | Item | Status (done / missing / partial / N/A) | Evidence (file:line) | Fix`.
Then:
- **Top 3 to fix before launch**, ranked by risk.
- **Needs a lawyer** — anything involving minors, health, finance, or unclear jurisdiction.

Do NOT edit code during the check. Offer fixes; implement only after the user confirms, on a separate branch.