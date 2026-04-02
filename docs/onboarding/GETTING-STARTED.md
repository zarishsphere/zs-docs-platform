# Getting Started with ZarishSphere

> **For:** New contributors (technical and non-technical)
> **Time to read:** 10 minutes

---

## Step 1 — Understand the Platform

Read these three documents in order:
1. [PHILOSOPHY.md](../platform/PHILOSOPHY.md) — the 8 non-negotiable commitments
2. [README.md](../../README.md) — platform overview
3. This document — then start contributing

---

## Step 2 — Set Up Your GitHub Account

1. Go to https://github.com and create a free account if you don't have one
2. Go to https://github.com/zarishsphere and click **"Follow"** on the organization
3. Request access: email platform@zarishsphere.com with your GitHub username

---

## Step 3 — Find Your First Contribution

### For non-coders:
- Look for Issues labeled `good-first-issue` in any docs repository
- Pick a documentation improvement, translation addition, or clinical protocol
- Comment on the issue: "I'd like to work on this"

### For developers:
- Look for Issues labeled `good-first-issue` in `zs-core-fhir-engine` or `zs-svc-patient`
- Ensure you have Go 1.26.1 installed: `go version`
- Clone the repository and read its README

---

## Step 4 — Understand the Repository Structure

```
github.com/zarishsphere/
├── .github/              ← Community health files, PR/issue templates
├── zs-docs-platform/     ← YOU ARE HERE — platform documentation
├── zs-docs-rfc/          ← Feature proposals (RFCs)
├── zs-docs-adr/          ← Architecture Decision Records
├── zs-docs-camm/         ← Country adoption tracking
├── zs-core-fhir-engine/  ← Primary FHIR R5 server (Go 1.26.1)
├── zs-svc-patient/       ← Patient registration service (Go 1.26.1)
├── zs-svc-encounter/     ← Encounter service (Go 1.26.1)
├── zs-ui-shell-clinical/ ← Clinical web app shell (Next.js 16.2)
├── zs-mobile-clinic/     ← Mobile clinical app (Flutter 3.41.2)
├── zs-content-forms-*/   ← Clinical forms (JSON Schema)
├── zs-data-*/            ← Terminologies, translations, FHIR profiles
├── zs-distro-bgd/        ← Bangladesh distribution
└── ... (211 total)
```

---

## Step 5 — Make Your First Contribution

### Option A: Edit a document (browser only)
1. Navigate to the file you want to improve
2. Click the pencil ✏️ icon
3. Make your changes
4. Write a commit message: `docs(platform): fix typo in CONTRIBUTING.md`
5. Click "Propose changes" → "Create pull request"

### Option B: Write a clinical protocol (browser only)
1. Go to https://github.com/zarishsphere/zs-content-protocols
2. Click "Add file" → "Create new file"
3. Name it: `protocols/your-protocol-name.md`
4. Write the protocol following the template in that repository
5. Open a PR for review

### Option C: Fix a code bug (requires local setup)
1. Read [LOCAL-SETUP.md](./LOCAL-SETUP.md)
2. Fork the repository
3. Create a branch: `fix/your-description`
4. Fix the bug, add a test
5. Open a PR with a clear description

---

## What Happens After You Open a PR

1. **Automated CI runs** — tests, lint, security scan (takes 2–5 minutes)
2. **CodeRabbit reviews** — AI code review comments appear automatically
3. **Owner review** — a human owner reviews within 1–5 business days
4. **Merge** — owner merges the PR
5. **Auto-deploy** — Argo CD deploys the change automatically

---

## Communication

| Purpose | Where |
|---------|-------|
| Propose changes | GitHub Issues (RFC template) |
| Bug reports | GitHub Issues (Bug Report template) |
| Questions about code | GitHub Discussions |
| Urgent platform issues | Email platform@zarishsphere.com |
| Clinical content questions | Email healthbgd@cpintl.org |

---

*Welcome to ZarishSphere. You are now part of building sovereign digital health infrastructure for millions of people.*
