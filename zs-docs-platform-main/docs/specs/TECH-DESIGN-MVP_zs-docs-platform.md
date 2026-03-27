# TECH-DESIGN-MVP — `zs-docs-platform`

> **Document:** Technical Design (MVP) | **Version:** 1.0.0-mvp
> **Repository:** [https://github.com/zarishsphere/zs-docs-platform](https://github.com/zarishsphere/zs-docs-platform)
> **Layer:** Layer 0 — Governance | **Catalog #:** 1
> **Language:** Markdown / Docusaurus (future) | **License:** Apache 2.0

---

## Technical Summary

**Master platform documentation repository — governance, RFCs, ADRs, contributor guides, and the CAMM model.**

This document defines the **technical architecture, implementation design, complete repository tree, and acceptance criteria** for the MVP of `zs-docs-platform`.

---

## Technical Approach — MVP

All MVP content is **pure Markdown** — no build tooling, no dependencies. Files are browseable directly on GitHub. Docusaurus is a **post-MVP** addition.

## File Format Standards

- All files: UTF-8 encoding, Unix line endings (LF)
- Headings: ATX style (`#`, `##`, `###`)
- Tables: GFM pipe tables
- Code blocks: fenced with language tag
- Links: relative paths within repo; absolute for cross-repo

## CI Pipeline (`.github/workflows/ci.yml`)

```yaml
name: CI
on: [push, pull_request]
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: markdownlint
        run: npx markdownlint-cli "**/*.md" --ignore node_modules
      - name: Check links (MVP only internal)
        run: npx markdown-link-check docs/**/*.md --config .mlc.json
```

## Content Governance

All content is written by platform owners directly in GitHub browser editor. No build tools required for MVP content. Docusaurus is deployed post-MVP.

## Directory Naming Convention

- `docs/` — user-facing documentation
- `templates/` — reusable templates for contributors
- `governance/` — process definitions (RFC, ADR, CAMM)
- `compliance/` — regulatory documentation

## MVP Complete Repository Tree

```
zs-docs-platform/
├── README.md
├── LICENSE
├── .gitignore
├── CHANGELOG.md
├── .github/
│   ├── CODEOWNERS
│   └── workflows/
│       ├── ci.yml                    # markdownlint + link check
│       └── pages.yml                 # Deploy to docs.zarishsphere.com
├── docs/
│   ├── governance/
│   │   ├── RFC-PROCESS.md
│   │   ├── ADR-PROCESS.md
│   │   ├── NAMING-STANDARDS.md
│   │   └── DECISION-LOG.md
│   ├── contributing/
│   │   ├── CONTRIBUTING.md
│   │   ├── CODE-STANDARDS.md
│   │   ├── COMMIT-CONVENTION.md
│   │   └── BRANCH-STRATEGY.md
│   ├── platform/
│   │   ├── PHILOSOPHY.md
│   │   ├── BLUEPRINT.md
│   │   ├── ARCHITECTURE.md
│   │   └── TECHNOLOGY-STACK.md
│   └── onboarding/
│       ├── GETTING-STARTED.md
│       ├── LOCAL-SETUP.md
│       └── NO-CODER-GUIDE.md
├── rfcs/
│   ├── README.md
│   ├── RFC-0001-naming-standards.md
│   └── RFC-0002-repo-structure.md
├── adrs/
│   ├── README.md
│   ├── ADR-0001-go-backend.md
│   ├── ADR-0002-fhir-r5.md
│   ├── ADR-0003-postgresql.md
│   ├── ADR-0004-nats-jetstream.md
│   ├── ADR-0005-valkey.md
│   ├── ADR-0006-opentofu.md
│   ├── ADR-0007-argocd.md
│   ├── ADR-0008-cilium.md
│   ├── ADR-0009-carbon-ds.md
│   ├── ADR-0010-cloudflare.md
│   ├── ADR-0011-microfrontend.md
│   └── ADR-0012-powersync.md
└── website/
    ├── docusaurus.config.js
    └── package.json
```

---


## Owners & Governance

| Role | GitHub Handle | Responsibility |
|------|--------------|----------------|
| Platform Lead | `@arwa-zarish` | Final approval, RFC votes |
| Technical Lead | `@code-and-brain` | Architecture, Go/TS review |
| DevOps Lead | `@DevOps-Ariful-Islam` | CI/CD, infra, deployment |
| Health Programs | `@BGD-Health-Program` | Clinical content, country programs |

**PR Policy:** All changes via Pull Request. Minimum 1 owner review. CI must pass. No direct commits to `main`.


---

## MVP Acceptance Checklist

- [ ] All MVP files exist in repository with real content (not placeholders)
- [ ] CI pipeline passes on `main` branch
- [ ] No secrets, credentials, or PHI committed
- [ ] README.md reflects current state with setup instructions
- [ ] CODEOWNERS file present
- [ ] All MVP functional requirements verified manually or via automated tests
- [ ] Linked to `CATALOGS.md` and `TODO.md` in `zs-docs-platform`

---

*This document is the authoritative MVP specification for `zs-docs-platform`.*
*Changes require a Pull Request with at least 1 owner approval.*
