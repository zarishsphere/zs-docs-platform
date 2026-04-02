# Branch Strategy

> **Version:** 2.0.0 | **Updated:** 2026-03-24

---

## Overview

ZarishSphere uses a **trunk-based development** model. There is one long-lived branch: `main`. Everything else is a short-lived feature branch that lives for hours to days, then merges back.

---

## Branch Naming Convention

```
{type}/{short-description}
```

| Type | When to use | Example |
|------|-------------|---------|
| `feat/` | New feature | `feat/patient-duplicate-detection` |
| `fix/` | Bug fix | `fix/fhir-audit-event-missing` |
| `docs/` | Documentation | `docs/add-camm-l2-checklist` |
| `refactor/` | Code restructure | `refactor/patient-repo-layer` |
| `test/` | Tests only | `test/fhir-engine-search-params` |
| `ci/` | CI/CD changes | `ci/add-trivy-scan` |
| `content/` | Clinical content | `content/add-phq9-form` |
| `chore/` | Maintenance | `chore/update-pgx-v5-7` |

**Rules:**
- All lowercase, hyphens only
- Maximum 50 characters
- Must include type prefix
- No personal names (no `ariful-fix`, no `my-feature`)

---

## Protected Branches

| Branch | Protection | Who can push |
|--------|-----------|-------------|
| `main` | ✅ Protected | No one (PR only) |
| `release/*` | ✅ Protected | Release manager bot only |

**`main` branch protections:**
- Require pull request before merging
- Require at least 1 approving review
- Require status checks to pass before merging (CI)
- Require branches to be up to date before merging
- Require conversation resolution before merging
- Restrict who can dismiss pull request reviews
- Do not allow force pushes
- Do not allow deletions

---

## Workflow

```
1. Create branch from main
   git checkout main && git pull
   git checkout -b feat/my-feature

2. Make changes and commit
   git add .
   git commit -m "feat(patient): add duplicate detection"

3. Push branch
   git push origin feat/my-feature

4. Open Pull Request on GitHub
   - Fill PR template completely
   - Link to Issue or RFC
   - CI runs automatically

5. Review and merge
   - At least 1 owner approves
   - All CI checks pass
   - Branch merged (squash or merge commit)
   - Branch deleted automatically
```

---

## Rules for All Contributors

1. **Never commit directly to `main`** — always use a PR
2. **Keep branches short-lived** — open PR within 1–2 days of creating branch
3. **One concern per branch** — don't mix feature work with unrelated fixes
4. **Delete branches after merge** — GitHub does this automatically if enabled
5. **Rebase or update before merge** — branch must be up to date with main

---

## For No-Code Contributors

When editing files in the GitHub browser:
1. GitHub automatically creates a new branch for you
2. It names it something like `arwa-zarish-patch-1`
3. That is fine — just open the PR and it will be merged
4. You don't need to manage branches manually

---

*Questions? Open an issue in zs-docs-platform.*
