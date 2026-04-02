# ZarishSphere Platform Philosophy

> **Document:** PHILOSOPHY.md | **Version:** 2.0.0
> **Governance:** This document changes only by unanimous owner vote.
> **Status:** Canonical — all platform decisions must respect these 8 commitments.

---

## The 8 Non-Negotiable Commitments

These are not guidelines. These are hard constraints. Every repository, every PR, every ADR, every RFC must respect all 8. If a proposal violates even one, it is rejected.

---

### Commitment 1: Zero Cost — Forever

> *"ZarishSphere will never pay a penny."*

Every tool, every service, every hosting provider, every dependency must have a **genuinely free tier** that covers ZarishSphere's operational needs. "Free trial" or "free for 30 days" is not acceptable.

**What this means in practice:**
- Use GitHub (free for public repos) not paid platforms
- Use Cloudflare free tier not AWS or GCP
- Use OpenTofu (MPL-2.0) not Terraform (BSL)
- Use Valkey (BSD-3) not Redis (SSPL)
- Self-host Keycloak, NATS, Prometheus — not paid SaaS equivalents
- Deploy to free-tier Oracle Cloud (Ampere ARM VMs) not AWS EC2

**Why:** ZarishSphere serves health programs in low- and middle-income countries. A platform that requires budget approval will never be adopted. Free means sovereign.

---

### Commitment 2: Open Source — Always

> *"Every repository is public. Every decision is an RFC."*

All code, all documentation, all configuration, all clinical content is published under Apache 2.0. No proprietary modules, no closed extensions, no private repositories for platform components.

**What this means in practice:**
- All 211 repositories are public on github.com/zarishsphere
- All architectural decisions are recorded in public ADRs
- All feature proposals go through public RFC process
- All platform meetings produce public written summaries

**Why:** Health data sovereignty requires platform sovereignty. A government or NGO cannot be sovereign on a platform whose source they cannot read, modify, and self-host.

---

### Commitment 3: Last Mile First

> *"Offline, low-bandwidth, low-end devices are first-class citizens."*

Every ZarishSphere feature must work without internet connectivity. The platform must run on a Raspberry Pi 5 (ARM64, 8GB RAM) at a rural health post with intermittent power.

**What this means in practice:**
- Go FHIR engine compiles to a single ARM64 binary with SQLite backend
- Flutter mobile apps use PowerSync for offline-first sync
- Tauri desktop embeds the Go FHIR binary — no cloud needed
- All forms, protocols, and terminologies are locally available after first sync
- Bandwidth-efficient sync protocols (delta sync, compression)
- No mandatory cloud dependency for core clinical workflows

**Why:** 70% of ZarishSphere's target population lives in areas with unreliable connectivity. Building cloud-first and "adding offline later" never works. Offline is the foundation.

---

### Commitment 4: Config ≠ Code

> *"Clinical content lives independently from the platform that runs it."*

Clinical forms, terminology concepts, treatment protocols, country configurations, and indicator definitions are **data**, not code. They live in their own repositories, have their own versioning, and can be updated without touching the platform.

**What this means in practice:**
- Clinical forms are JSON Schema files in `zs-content-forms-*`
- Terminology data is in `zs-data-*` repositories
- Country configurations are in `zs-distro-*` repositories
- Platform code never hardcodes clinical logic
- A clinician with no coding skills can update a form via GitHub browser

**Why:** Clinical content changes 10× more often than platform code. Forms need clinical review, not code review. Country configs need local health authority approval, not a developer.

---

### Commitment 5: Document Governs

> *"ADRs, RFCs, PRs — nothing happens outside of GitHub."*

No verbal decisions. No WhatsApp agreements. No email-only approvals. Every decision that affects the platform is:
1. Proposed as an RFC (with full context and options)
2. Discussed openly in GitHub
3. Decided by owner vote
4. Recorded as an ADR if architectural
5. Implemented only through a Pull Request

**What this means in practice:**
- "We agreed on a call to use MySQL" is NOT a valid reason to use MySQL
- "ADR-0003 explicitly chose PostgreSQL" IS a valid reason to stay on PostgreSQL
- All CI/CD changes go through PR review
- Platform owners can onboard a country entirely through GitHub browser

**Why:** Tribal knowledge kills platforms. When the original team members move on, the platform must continue. Written decisions survive people.

---

### Commitment 6: Single Source of Truth (SSOT)

> *"If it's not documented, it doesn't exist. If it exists, it must be automatable."*

There is one canonical version of every configuration, every schema, every policy. CATALOGS.md is the source of truth for repositories. This PHILOSOPHY.md is the source of truth for commitments. ADRs are the source of truth for architectural decisions.

**What this means in practice:**
- CATALOGS.md defines all repositories — if a repo isn't there, it shouldn't exist
- Go struct tags are the source of truth for API field names
- JSON Schema files are the source of truth for form fields
- OpenAPI 3.1 YAML is the source of truth for API contracts

**Why:** Multiple copies of truth become contradictory. Contradictions cause bugs. Bugs cause patient harm.

---

### Commitment 7: No-Coder Friendly

> *"GUI-first, template-driven, fully automatable. No terminal required for operators."*

Platform owners, country program managers, and clinical staff must be able to:
- Review and approve PRs
- Write and submit RFCs
- Update clinical content
- Monitor system health
- Onboard new countries

All without opening a terminal.

**What this means in practice:**
- GitHub browser is the primary interface for platform governance
- Automation agents (bots) handle what would otherwise require command-line work
- Issue templates and PR templates guide contributors
- Argo CD handles deployments automatically after merge
- Renovate handles dependency updates automatically
- Dashboard URLs are bookmarkable and require no login for read-only view

**Why:** ZarishSphere is governed by health professionals, not software engineers. The platform must be governable by the people who understand health programs.

---

### Commitment 8: FHIR R5 Native

> *"All clinical data is FHIR R5. No exceptions."*

Every piece of clinical information stored in ZarishSphere is a FHIR R5 resource. Not a custom schema, not a proprietary format, not "FHIR-compatible." Actual FHIR R5 resources, stored as JSONB in PostgreSQL, accessible via a conformant FHIR R5 REST API.

**What this means in practice:**
- Patient records are `Patient` resources
- Vitals are `Observation` resources with LOINC codes
- Diagnoses are `Condition` resources with ICD-11 codes
- Appointments are `Appointment` resources
- All clinical APIs follow FHIR R5 REST specification
- FHIR R4 bridge exists for partner systems, but storage is always R5
- All FHIR resources validated against ZarishSphere profiles on write

**Why:** FHIR R5 is the current international standard for health data interoperability. A platform that doesn't speak FHIR cannot exchange data with national HIS, laboratory systems, or partner NGOs.

---

## How These Commitments Are Enforced

| Commitment | Enforcement Mechanism |
|-----------|----------------------|
| Zero Cost | License audit on every new dependency PR |
| Open Source | All repos are public; no private forks allowed |
| Last Mile First | Offline tests in CI pipeline for mobile and desktop |
| Config ≠ Code | Clinical content lives in separate repositories |
| Document Governs | ADR required for any architecture change |
| SSOT | CATALOGS.md validated in CI against actual repos |
| No-Coder Friendly | Platform bot handles automation; no CLI required |
| FHIR R5 | FHIR validation in CI; HAPI validator on all resources |

---

*This document was last updated 2026-03-24.*
*Changes require unanimous vote of all 4 platform owners.*
