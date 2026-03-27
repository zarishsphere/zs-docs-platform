# PRD — `zs-docs-platform`

> **Document Class:** PRD | **Version:** 1.0.0 | **Status:** Bootstrapping
> **Repository:** [https://github.com/zarishsphere/zs-docs-platform](https://github.com/zarishsphere/zs-docs-platform)
> **Layer:** Layer 0 — Governance | **Catalog #:** 1
> **License:** Apache 2.0 | **Governance:** RFC-0001

---

## 1. Overview

Master platform documentation repository. Contains CONTRIBUTING.md, RFC process, ADR index, naming standards, platform philosophy, and the governance model for the entire ZarishSphere organization.

---

## 2. Repository Metadata

- **Name:** `zs-docs-platform`
- **Organization:** [https://github.com/zarishsphere](https://github.com/zarishsphere)
- **Language / Runtime:** Markdown / Docusaurus
- **Visibility:** Public
- **License:** Apache 2.0
- **Default Branch:** `main`
- **Branch Protection:** Required (2-owner review for critical paths)

---

## 3. Platform Context

This repository is part of the **ZarishSphere** sovereign digital health operating platform — a free, open-source, FHIR R5-native system for South and Southeast Asia.

**Non-negotiable constraints:**
- Zero cost — all tooling must use genuinely free tiers
- FHIR R5 native — all clinical data modelled as FHIR R5 resources
- Offline-first — must work without network connectivity
- No-coder friendly — GUI-first, template-driven
- Documentation as Code — all decisions in GitHub

---

## 4. Goals & Objectives

- Provide a single authoritative source of truth for platform governance
- Define and enforce naming conventions across all 212 repositories
- Host the RFC and ADR processes that govern all architectural decisions
- Publish documentation to docs.zarishsphere.com via GitHub Pages

## 5. Functional Requirements

| ID | Requirement | Priority |
|----|------------|---------|
| F-01 | RFC process documentation with templates and decision log | P0 |
| F-02 | ADR log linking to all Architecture Decision Records | P0 |
| F-03 | Contributor guide (CONTRIBUTING.md) | P0 |
| F-04 | Repository naming convention spec | P0 |
| F-05 | Platform philosophy and 8 non-negotiable commitments | P0 |
| F-06 | CAMM (Country Adoption Maturity Model) documentation | P1 |
| F-07 | Docusaurus site published to docs.zarishsphere.com | P1 |
| F-08 | Automated link checking on every PR | P2 |

## 6. Repository Tree

```
zs-docs-platform/
├── README.md                          # Platform overview and quick-start
├── LICENSE                            # Apache 2.0
├── .github/
│   ├── CODEOWNERS                     # @arwa-zarish @code-and-brain
│   └── workflows/
│       ├── ci.yml                     # Link check + markdownlint
│       └── pages.yml                  # Deploy to docs.zarishsphere.com
├── docs/
│   ├── governance/
│   │   ├── README.md                  # Governance overview
│   │   ├── RFC-PROCESS.md             # How to propose + vote on an RFC
│   │   ├── ADR-PROCESS.md             # How to write and log an ADR
│   │   ├── NAMING-STANDARDS.md        # zs-{layer}-{module} convention
│   │   └── DECISION-LOG.md            # Master index of all RFCs and ADRs
│   ├── contributing/
│   │   ├── CONTRIBUTING.md            # Full contributor guide
│   │   ├── CODE-STANDARDS.md          # Go, TypeScript, clinical content rules
│   │   ├── COMMIT-CONVENTION.md       # Conventional Commits reference
│   │   └── BRANCH-STRATEGY.md        # Branch naming and protection rules
│   ├── platform/
│   │   ├── PHILOSOPHY.md              # 8 non-negotiable commitments
│   │   ├── BLUEPRINT.md               # Platform overview (copy of .github)
│   │   ├── ARCHITECTURE.md            # Technical architecture (copy of .github)
│   │   ├── TECHNOLOGY-STACK.md        # Full tech stack with versions
│   │   └── FREE-TIER-MAP.md           # All services and their free-tier guarantees
│   ├── onboarding/
│   │   ├── GETTING-STARTED.md         # First-time contributor guide
│   │   ├── LOCAL-SETUP.md             # Dev environment setup
│   │   └── NO-CODER-GUIDE.md          # Platform owner daily workflow
│   └── camm/
│       ├── OVERVIEW.md                # CAMM L0–L5 model
│       ├── L0-INTEREST.md             # Interest stage checklist
│       ├── L1-FOUNDATION.md           # Foundation stage checklist
│       ├── L2-ADOPTION.md             # Adoption stage checklist
│       ├── L3-INTEGRATION.md          # Integration stage checklist
│       ├── L4-MATURITY.md             # Maturity stage checklist
│       └── L5-SOVEREIGNTY.md          # Sovereignty stage checklist
├── rfcs/
│   ├── README.md                      # RFC index
│   ├── RFC-0001-naming-standards.md   # Accepted
│   ├── RFC-0002-repo-structure.md     # Accepted
│   └── RFC-0003-automation.md         # Draft
├── adrs/
│   ├── README.md                      # ADR index
│   ├── ADR-0001-go-backend.md         # Go as primary language
│   ├── ADR-0002-fhir-r5.md            # FHIR R5 as clinical model
│   ├── ADR-0003-postgresql.md         # PostgreSQL as only database
│   ├── ADR-0004-nats-jetstream.md     # NATS for messaging
│   ├── ADR-0005-valkey.md             # Valkey over Redis
│   ├── ADR-0006-opentofu.md           # OpenTofu over Terraform
│   ├── ADR-0007-argocd.md             # Argo CD for GitOps
│   ├── ADR-0008-cilium.md             # Cilium over Istio
│   ├── ADR-0009-carbon-ds.md          # Carbon Design System
│   ├── ADR-0010-cloudflare.md         # Cloudflare as edge
│   ├── ADR-0011-microfrontends.md     # Microfrontend architecture
│   └── ADR-0012-powersync.md          # PowerSync for mobile offline
├── website/
│   ├── docusaurus.config.js           # Docusaurus 3 config
│   ├── package.json                   # Node.js dependencies
│   ├── sidebars.js                    # Navigation structure
│   └── static/
│       └── img/
│           └── logo.svg               # ZarishSphere logo
└── .markdownlint.yml                  # Markdown style rules
```

## 7. Dependencies

- Docusaurus 3 (MIT) — documentation site generator
- markdownlint-cli (MIT) — markdown linting
- GitHub Pages (free) — hosting
- GitHub Actions (free for public) — CI/CD

## 8. Key Interfaces

- Publishes to `docs.zarishsphere.com` via GitHub Pages
- All 212 repos reference this repo for CONTRIBUTING.md
- RFC process triggers issues in `zs-docs-rfc`

## 9. Ownership & Governance

| Role | GitHub User |
|------|-------------|
| Platform Lead | `@arwa-zarish` |
| Technical Lead | `@code-and-brain` |
| DevOps Lead | `@DevOps-Ariful-Islam` |
| Health Programs | `@BGD-Health-Program` |

All changes go through Pull Request → 1+ owner review → CI pass → merge.
Breaking changes require RFC + ADR.

---

## 10. Definition of Done

- [ ] All listed files exist with content
- [ ] CI pipeline passes (test + lint + security)
- [ ] README.md reflects current state
- [ ] OpenAPI / AsyncAPI spec present (services only)
- [ ] At least 1 integration test using testcontainers-go (Go) or Playwright (UI)
- [ ] No secrets committed (GitGuardian verified)
- [ ] CODEOWNERS file present
- [ ] Linked to CATALOGS.md and TODO.md

---

*This PRD is the canonical source of truth for this repository's purpose, structure, and requirements.*
*Changes require a PR against this file with owner approval.*
