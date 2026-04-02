# zs-docs-platform

> **Master Platform Documentation** | ZarishSphere Governance Layer 0
> **Repository:** https://github.com/zarishsphere/zs-docs-platform
> **Catalog #:** 1 | **Status:** Active | **License:** Apache 2.0

---

## What This Repository Is

This is the **single source of truth** for the entire ZarishSphere platform. Every decision, every standard, every process, every contribution rule — it starts and ends here.

If something is not documented in this repository, it does not officially exist in ZarishSphere.

---

## Quick Navigation

| Document | What It Covers |
|----------|---------------|
| [CONTRIBUTING.md](./docs/contributing/CONTRIBUTING.md) | How to contribute to ZarishSphere |
| [CODE-STANDARDS.md](./docs/contributing/CODE-STANDARDS.md) | Go, TypeScript, and clinical content coding rules |
| [COMMIT-CONVENTION.md](./docs/contributing/COMMIT-CONVENTION.md) | How to write commit messages |
| [BRANCH-STRATEGY.md](./docs/contributing/BRANCH-STRATEGY.md) | Branch naming and protection rules |
| [PHILOSOPHY.md](./docs/platform/PHILOSOPHY.md) | 8 non-negotiable platform commitments |
| [GETTING-STARTED.md](./docs/onboarding/GETTING-STARTED.md) | For new contributors |
| [NO-CODER-GUIDE.md](./docs/onboarding/NO-CODER-GUIDE.md) | For platform owners who don't code |
| [RFC Process](./rfcs/README.md) | How to propose changes |
| [ADR Index](../zs-docs-adr/) | All architectural decisions |

---

## Platform at a Glance

ZarishSphere is a **free, open-source, FHIR R5-native digital health operating platform** for South and Southeast Asia, targeting Bangladesh 🇧🇩, India 🇮🇳, Myanmar 🇲🇲, Pakistan 🇵🇰, and Thailand 🇹🇭.

**Technology Stack (March 2026):**

| Layer | Technology | Version |
|-------|-----------|---------|
| Backend | Go | 1.26.1 |
| Database | PostgreSQL + TimescaleDB | 18.3 + 2.25 |
| Messaging | NATS JetStream | 2.12.5 |
| Cache | Valkey | 9.0.3 |
| Search | Typesense | 30.1 |
| Auth | Keycloak | 26.5.6 |
| Frontend | React + Next.js | 19.x + 16.2 |
| Language | TypeScript | 6.0 |
| Mobile | Flutter + Dart | 3.41.2 + 3.11 |
| Desktop | Tauri | 2.3 |
| IaC | OpenTofu | 1.11.x |
| GitOps | Argo CD | 3.3.x |
| Orchestration | Kubernetes | 1.35 |

---

## Repository Owners

| Role | GitHub Handle | Responsibility |
|------|--------------|----------------|
| Platform Lead | `@arwa-zarish` | Final approval, RFC votes |
| Technical Lead | `@code-and-brain` | Architecture, Go/TS review |
| DevOps Lead | `@DevOps-Ariful-Islam` | CI/CD, infra, deployment |
| Health Programs | `@BGD-Health-Program` | Clinical content, country programs |

All changes require a Pull Request. Minimum 1 owner review. CI must pass before merge.

---

## How To Contribute (No Coding Required)

1. Go to [Issues](https://github.com/zarishsphere/zs-docs-platform/issues) → New Issue
2. Choose "RFC / Feature Request" template
3. Write your idea in plain language
4. Submit — owners will review and respond within 5 business days

See [NO-CODER-GUIDE.md](./docs/onboarding/NO-CODER-GUIDE.md) for more.
