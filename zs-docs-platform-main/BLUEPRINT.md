# ZarishSphere Platform Blueprint

> **Document Class:** BLUEPRINT | **Version:** 1.0.0 | **Status:** Canonical
> **Owner:** Platform Architecture Group | **Governance:** RFC-0001
> **Organization:** [github.com/orgs/zarishsphere](https://github.com/orgs/zarishsphere)

---

## 1. What Is ZarishSphere?

ZarishSphere (`zs`) is a **sovereign, multi-national digital health operating platform** — a free, open-source, documentation-driven system designed to coordinate, govern, and future-proof health information across South and Southeast Asia.

It is not a single application. It is a **platform of platforms** — a GitHub organization that functions as a digital health operating system. Every operational act begins and ends in a GitHub repository. It coordinates existing systems (DHIS2, OpenMRS, Go.Data, EWARS, OpenIMIS) rather than replacing them.

**ZarishSphere is:**
- A sovereign operating system for digital health programs
- A FHIR R5-native interoperability fabric
- A no-coder-friendly GitHub organization with full automation
- An integrated suite of EMR + EHR + HIS + ERP capabilities
- A last-mile-first, offline-capable clinical platform
- A completely zero-cost, zero-vendor-lock platform

---

## 2. Non-Negotiable Commitments

| # | Commitment | Description |
|---|-----------|-------------|
| 1 | **Zero Cost** | Every tool, host, service must have a genuinely free tier. ZarishSphere will never pay a penny. |
| 2 | **Open Source** | Every repository is public. Every decision is an RFC. Every dollar spent is zero. |
| 3 | **Last Mile First** | Offline, low-bandwidth, low-end devices are first-class. Full FHIR engine runs on Raspberry Pi 5. |
| 4 | **Config ≠ Code** | Clinical content (forms, concepts, terminologies) lives in independent repos. |
| 5 | **Document Governs** | ADRs, RFCs, PRs — nothing happens outside of GitHub. |
| 6 | **SSOT** | Single Source of Truth drives everything. If it's not documented, it doesn't exist. |
| 7 | **No-Coder Friendly** | GUI-first, template-driven, fully automatable. No terminal required for operators. |
| 8 | **FHIR R5** | All clinical data is FHIR R5-native with an R4 bridge for partner systems. |

---

## 3. Platform Architecture (Five Layers)

```
┌─────────────────────────────────────────────────────────────────┐
│  LAYER 0: GOVERNANCE & SSOT                                     │
│  RFCs · ADRs · CAMM · DaC · Security Policy · Donor Reports    │
├─────────────────────────────────────────────────────────────────┤
│  LAYER 1: PRESENTATION (SaaS)                                   │
│  Web App (Next.js MFEs) · Mobile (Flutter) · Desktop (Tauri)   │
├─────────────────────────────────────────────────────────────────┤
│  LAYER 2: API & INTEROPERABILITY (PaaS)                         │
│  FHIR R5 Engine · OpenAPI · SMART on FHIR · NATS Events        │
│  DHIS2 · HL7v2 · DICOM · Go.Data · OpenIMIS bridges           │
├─────────────────────────────────────────────────────────────────┤
│  LAYER 3: DATA & ANALYTICS                                      │
│  PostgreSQL (FHIR JSONB) · TimescaleDB · Typesense · Grafana   │
├─────────────────────────────────────────────────────────────────┤
│  LAYER 4: INFRASTRUCTURE (IaC)                                  │
│  OpenTofu · Helm · Argo CD · Kubernetes · Cloudflare · GHCR   │
└─────────────────────────────────────────────────────────────────┘
```

---

## 4. Deployment Topology

### Configuration Inheritance (3-Level Cascade)

```
zs-distro-core           (Global defaults, base FHIR IG)
    └── zs-distro-bgd    (Bangladesh national config)
            └── zs-infra-bgd/cxb/camp-1w-health-post
            └── zs-infra-bgd/cxb/camp-4-health-post
            └── zs-infra-bgd/dhaka-sadar-hospital
```

Every site inherits from its country distro, which inherits from core. Overrides are additive-only — no destructive config inheritance.

### Supported Deployment Modes

| Mode | Description | Target |
|------|-------------|--------|
| Cloud (K8s) | Multi-tenant, fully managed | National programs |
| Self-hosted | On-prem K8s, OpenTofu IaC | Government data centers |
| Hybrid | Cloud control plane + on-prem data | Mixed environments |
| Offline (Desktop) | Tauri + embedded Go FHIR server | Disconnected clinics |
| Offline (Mobile) | Flutter + PowerSync SQLite | CHW field workers |
| Edge (Raspberry Pi) | Go binary + SQLite | Health posts, last-mile |

---

## 5. Clinical Domain Coverage

| Domain | Services | Forms | Standards |
|--------|----------|-------|-----------|
| Primary Care | patient, encounter, observation, condition | Core clinical forms | FHIR R5 |
| Maternity (ANC/PNC) | maternity | ANC forms, partograph | WHO ANC guidelines |
| Child Health | child-health, immunization | Growth charts, EPI | WHO Child Growth Standards |
| Nutrition | nutrition | MUAC, CMAM, MIYCN | SMART indicators |
| Mental Health | mental-health | PHQ-9, GAD-7, MHPSS | WHO mhGAP |
| Communicable Disease | communicable-disease, ewars | TB, malaria, cholera | IDSR, WHO IHR |
| Laboratory | laboratory | Lab orders, results | LOINC 2.78 |
| Pharmacy | pharmacy | Medication orders, dispensing | RxNorm |
| Radiology | radiology | DICOM worklists | DICOM 3.0 |
| NCD | condition, care-plan | HTN, DM screening | WHO NCD guidelines |
| Emergency | encounter, procedure | Triage, trauma | START/SALT |
| Surveillance | surveillance, indicator, reporting | Aggregate, case-based | DHIS2, WHO |
| Community Health | (mobile) | CHW forms, surveys | WASH, SBCC |
| Refugee/Humanitarian | distro-humanitarian | UNHCR, SPHERE forms | SPHERE Handbook |

---

## 6. Interoperability Matrix

| System | Direction | Protocol | Status |
|--------|-----------|----------|--------|
| DHIS2 | Bidirectional | DHIS2 Web API v38, FHIR | Planned |
| OpenMRS | Bidirectional | FHIR R4/R5, REST | Planned |
| Go.Data | Export | REST API | Planned |
| EWARS | Import/Export | REST, CSV | Planned |
| OpenIMIS | Bidirectional | REST, FHIR | Planned |
| HL7 v2 ADT/Lab | Inbound | HL7 v2.9 MLLP | Planned |
| DICOM/PACS | Bidirectional | DICOM 3.0 | Planned |
| ABDM (India) | Bidirectional | ABDM APIs, FHIR | Planned |
| WHO ICD-11 | Inbound | REST API | Planned |
| SNOMED | Inbound | RF2, REST | Planned |

---

## 7. Free-Tier Resource Map

All services used by ZarishSphere and their free-tier guarantees:

| Service | Free Tier | Notes |
|---------|-----------|-------|
| GitHub (Public Repos) | Unlimited repos, unlimited collaborators | Core platform |
| GitHub Actions | 2,000 min/month | CI/CD backbone |
| GitHub Pages | Free for public repos | Docs hosting |
| GHCR | Free for public images | Container registry |
| Cloudflare (Free) | DNS, CDN, SSL, Workers (100k req/day), R2 (10GB) | Edge layer |
| Cloudflare Email Routing | Free redirect rules | Email handling |
| Vercel (Hobby) | Unlimited deployments for public repos | Frontend hosting |
| Render (Free) | 750 hrs/month compute | Dev/staging services |
| Fly.io (Free) | 3 shared VMs, 3GB storage | Small services |
| Supabase (Free) | 500MB PostgreSQL, 1GB storage | Dev database |
| Neon (Free) | PostgreSQL serverless, 512MB | Branching DBs |
| Upstash (Free) | Redis (10k cmd/day), Kafka | Serverless caching |
| Typesense Cloud (Free) | 10k docs | Search dev tier |
| Keycloak (Self-hosted) | Unlimited | Auth, free OSS |
| HashiCorp Vault (Self-hosted) | Unlimited | Secrets, free OSS |
| OpenTofu | Free forever | IaC, OSS fork |
| Argo CD | Free, CNCF | GitOps |
| Grafana OSS | Free forever | Observability |
| Prometheus | Free forever | Metrics |
| NATS (Self-hosted) | Free forever | Messaging |

---

## 8. Technology Stack Snapshot

### Backend (Go)
- **Language:** Go 1.26.1 (Green Tea GC)
- **FHIR:** fhir-toolbox-go (damedic) + gofhir-models (fastenhealth)
- **HTTP:** chi v5.2.1
- **DB:** PostgreSQL 18.3 + pgx v5.7.2
- **Time-series:** TimescaleDB 2.26.0
- **Cache:** Valkey 9.0.3
- **Events:** NATS 2.12.5 + JetStream
- **Search:** Typesense 30.1
- **Auth:** Keycloak 26.5.6
- **Tasks:** Asynq 0.24
- **Secrets:** HashiCorp Vault 1.18
- **Mesh:** Cilium 1.17

### Frontend (TypeScript)
- **Framework:** React 19.2.4 + Next.js 15.3
- **Language:** TypeScript 5.8
- **Design System:** Carbon DS 11.x (WCAG 2.2 AA)
- **Forms:** React Hook Form 7.54 + Zod 3.24
- **State:** Zustand 5.0
- **Offline:** Dexie.js 4.0
- **Charts:** Apache ECharts 5.6
- **Maps:** OpenLayers 10.3
- **i18n:** i18next 24.2
- **Bundler:** Vite 6.2

### Mobile (Flutter)
- **Framework:** Flutter 3.29 (Dart 3.7)
- **State:** Riverpod 2.6
- **Offline Sync:** PowerSync 1.x (self-hosted)

### Desktop (Tauri)
- **Framework:** Tauri 2.3 (Rust-based)
- **FHIR:** Embedded Go binary (full offline FHIR server)

### Infrastructure
- **Containers:** Docker 27.5
- **Orchestration:** Kubernetes 1.35
- **IaC:** OpenTofu 1.9.1
- **GitOps:** Argo CD 2.14
- **CI/CD:** GitHub Actions (free tier)
- **CDN/Edge:** Cloudflare (free tier)
- **Storage:** Cloudflare R2 (10GB free)
- **Registry:** GHCR (free for public)

---

## 9. Data Governance Principles

1. **Country Data Sovereignty:** Each country owns its data. Cross-border data sharing requires explicit consent and bilateral agreement.
2. **GDPR/HIPAA by Design:** Field-level encryption, audit logs, consent management, right-to-erasure workflows.
3. **Minimum Necessary:** Only collect what is clinically necessary for the program context.
4. **Pseudonymization:** PHI is pseudonymized in analytics and reporting pipelines.
5. **Offline Privacy:** All offline data is encrypted at rest on device.
6. **Audit Everything:** Every read/write/delete of PHI generates a FHIR AuditEvent.

---

## 10. Country Adoption Maturity Model (CAMM)

| Level | Stage | Description | Gates |
|-------|-------|-------------|-------|
| L0 | **Interest** | Country engagement, needs assessment | Signed MOU |
| L1 | **Foundation** | Distro fork, facility registry, user training | 1 facility live |
| L2 | **Adoption** | Core clinical modules, basic reporting | 10+ facilities, DHIS2 connected |
| L3 | **Integration** | National system bridges, interoperability | MOH integration live |
| L4 | **Maturity** | Full EHR, analytics, public health surveillance | National rollout |
| L5 | **Sovereignty** | Country self-manages, contributes back | Own DevOps team |

---

## 11. Governance Model

All platform decisions follow a documented process:

```
Idea → RFC Draft → Community Comment (14 days) → RFC Accepted/Rejected
                                                        ↓
                                              ADR Created (if architectural)
                                                        ↓
                                              PR Raised → Review → Merge
                                                        ↓
                                              Automated deployment via Argo CD
```

No undocumented changes. No verbal decisions. No shadow configs.

---

## 12. Owners & Stewards

| Role | GitHub User | Email |
|------|-------------|-------|
| Platform Lead | `arwa-zarish` | zarishsphere@gmail.com |
| Technical Lead | `code-and-brain` | code.and.brain@gmail.com |
| DevOps Lead | `DevOps-Ariful-Islam` | dev43ariful@gmail.com |
| Health Programs | `BGD-Health-Program` | healthbgd@cpintl.org |

---

> This blueprint is the canonical reference document. Changes require RFC + PR + 2 owner approvals.
> **Next:** See `ARCHITECTURE.md` for technical deep-dive, `CATALOGS.md` for full repository list.
