# PRD-MVP — `zs-docs-platform`

> **Document:** Product Requirements (MVP) | **Version:** 1.0.0-mvp
> **Repository:** [https://github.com/zarishsphere/zs-docs-platform](https://github.com/zarishsphere/zs-docs-platform)
> **Layer:** Layer 0 — Governance | **Catalog #:** 1
> **Language:** Markdown | **License:** Apache 2.0

---

## Executive Summary

**Master platform documentation repository — governance, RFCs, ADRs, contributor guides, and the CAMM model.**

This document defines the **Minimum Viable Product (MVP)** scope for `zs-docs-platform` within the ZarishSphere sovereign digital health platform. It covers what must be built first, acceptance criteria, user stories, and the complete repository file structure.


### Platform Non-Negotiables (apply to every repository)

| Constraint | Rule |
|-----------|------|
| **Zero Cost** | All tooling, hosting, and services must use genuinely free tiers |
| **Open Source** | Apache 2.0 license; all code public |
| **FHIR R5 Native** | All clinical data modelled as FHIR R5 resources |
| **Offline-First** | Must function without network connectivity |
| **No-Coder Friendly** | GUI-first, template-driven, automatable |
| **Documentation as Code** | All decisions in GitHub via RFC/ADR |
| **Multi-tenant** | tenant_id scoping on all data operations |
| **HIPAA/GDPR** | AuditEvent on all PHI access; field-level encryption |

---

## Problem Statement

Without this repository, platform contributors and country program teams lack the governance documentation they need. Decisions become tribal knowledge, standards are inconsistently applied, and the platform cannot scale across countries.

## MVP Goals

1. Publish RFC-0001 and RFC-0002 as accepted
2. ADR-0001 through ADR-0012 fully written and indexed
3. CONTRIBUTING.md complete with code standards and commit convention
4. Platform philosophy (8 commitments) documented
5. Docusaurus site skeleton committed (content populates later)

## MVP Functional Requirements

| ID | Requirement | Acceptance Criteria | Priority |
|----|------------|---------------------|---------|
| M-01 | RFC-0001 (Naming Standards) written and accepted | File exists, labeled rfc:accepted | P0 |
| M-02 | RFC-0002 (Repo Structure) written and accepted | File exists, labeled rfc:accepted | P0 |
| M-03 | All 12 ADRs written with context/options/decision | adrs/ADR-000x-*.md all exist | P0 |
| M-04 | CONTRIBUTING.md covers Go, TS, and clinical content standards | File >300 lines with all sections | P0 |
| M-05 | GETTING-STARTED.md for no-coders | File exists with step-by-step guide | P0 |
| M-06 | markdownlint CI passes | CI workflow passes on main | P1 |

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
