# ZarishSphere Platform — Complete Repository Catalog

> **Version:** 1.0.0 | **Status:** Bootstrapping | **Governance:** RFC-0001
> **Organization:** [github.com/orgs/zarishsphere](https://github.com/orgs/zarishsphere)
> **Domain:** zarishsphere.com | **License:** Apache 2.0 (unless noted)

---

## Naming Convention

All repositories follow the canonical `zs-<layer>-<module>` pattern:

```
zs-{layer}-{module}[-{submodule}]

Layers:
  core        → Platform kernel, FHIR engine, shared libraries
  svc         → Backend microservices (Go)
  ui          → Frontend microfrontends (React/Next.js)
  mobile      → Flutter mobile apps
  desktop     → Tauri desktop apps
  iac         → Infrastructure as Code (OpenTofu, Helm, K8s)
  data        → Data as Code (terminologies, concepts, forms)
  content     → Clinical content (forms, workflows, protocols)
  ops         → Operational tooling, bots, automation
  docs        → Documentation, RFCs, ADRs, standards
  pkg         → Shared packages / libraries
  distro      → Country/program distros (BGD, MMR, IND, PAK, THA)
  agent       → GitHub Automation Agents / Bots
  infra       → Live infrastructure state (per environment)
```

---

## Layer 0 — Platform Governance & Meta

| # | Repository | Description | Status |
|---|-----------|-------------|--------|
| 1 | `zs-docs-platform` | Master platform documentation, RFCs, ADRs, contributor guides, governance | 🔴 Bootstrap |
| 2 | `zs-docs-standards` | Clinical and technical standards: FHIR profiles, IGs, terminology policies | 🔴 Bootstrap |
| 3 | `zs-docs-runbooks` | Operational runbooks: deployments, incidents, on-call, backup, recovery | 🔴 Bootstrap |
| 4 | `zs-docs-adr` | Architecture Decision Records (ADR) log — all major decisions | 🔴 Bootstrap |
| 5 | `zs-docs-rfc` | Request for Comments — open proposals, drafts, community input | 🔴 Bootstrap |
| 6 | `zs-docs-camm` | Country Adoption Maturity Model (CAMM) — onboarding frameworks | 🔴 Bootstrap |
| 7 | `zs-docs-donor` | Donor-facing reports, grant deliverables, public impact dashboards | 🔴 Bootstrap |
| 8 | `zs-docs-security` | Security policies, HIPAA/GDPR compliance documentation, threat models | 🔴 Bootstrap |
| 9 | `.github` | GitHub org-level community health files, PR templates, issue templates | 🔴 Bootstrap |
| 10 | `zs-ops-github-bot` | GitHub App bot: auto-labeling, PR validation, RFC state machine, merge automation | 🔴 Bootstrap |

---

## Layer 1 — Core Platform Kernel

### 1A. FHIR Engine & Runtime

| # | Repository | Language | Description |
|---|-----------|----------|-------------|
| 11 | `zs-core-fhir-engine` | Go | Primary FHIR R5 server — REST, search, history, validation, FHIRPath. Runs on Raspberry Pi 5. |
| 12 | `zs-core-fhir-r4-bridge` | Go | R4↔R5 translation layer — bridges 73% of partner systems still on R4 |
| 13 | `zs-core-fhir-validator` | Go | Standalone FHIR resource validator with profile/IG support |
| 14 | `zs-core-fhir-subscriptions` | Go | FHIR R5 topic-based subscription engine via NATS JetStream |
| 15 | `zs-core-fhirpath` | Go | FHIRPath 2.0 evaluator — used in validation, CDS, search |
| 16 | `zs-core-cds-hooks` | Go | CDS Hooks 2.0 service — clinical decision support integration |

### 1B. Shared Go Libraries (Packages)

| # | Repository | Language | Description |
|---|-----------|----------|-------------|
| 17 | `zs-pkg-go-fhir` | Go | Shared Go FHIR types, builders, and utility functions (wraps damedic/fhir-toolbox-go) |
| 18 | `zs-pkg-go-auth` | Go | OIDC/SMART on FHIR token validation, JWT parsing, scope enforcement |
| 19 | `zs-pkg-go-db` | Go | PostgreSQL connection pool, pgx v5 wrapper, JSONB helpers |
| 20 | `zs-pkg-go-cache` | Go | Valkey/Redis client wrapper with FHIR-aware TTL patterns |
| 21 | `zs-pkg-go-messaging` | Go | NATS JetStream client — pub/sub, durable consumers, FHIR events |
| 22 | `zs-pkg-go-audit` | Go | FHIR AuditEvent generation, structured zerolog, compliance logging |
| 23 | `zs-pkg-go-telemetry` | Go | OpenTelemetry 1.40 — traces, metrics, Prometheus exporter |
| 24 | `zs-pkg-go-config` | Go | Viper-based config loader — YAML, env vars, Vault secrets |
| 25 | `zs-pkg-go-migration` | Go | golang-migrate wrapper — idempotent PostgreSQL schema migrations |
| 26 | `zs-pkg-go-testing` | Go | testcontainers-go fixtures, FHIR test helpers, integration test utilities |
| 27 | `zs-pkg-go-i18n` | Go | Multi-language support: EN, BN, MY, UR, HI, TH — server-side |
| 28 | `zs-pkg-go-crypto` | Go | Encryption utilities: field-level encryption, GDPR pseudonymization |

### 1C. Shared Frontend Libraries (Packages)

| # | Repository | Language | Description |
|---|-----------|----------|-------------|
| 29 | `zs-pkg-ui-design-system` | TypeScript | ZarishSphere design system — Carbon DS extensions, WCAG 2.2 AA |
| 30 | `zs-pkg-ui-fhir-hooks` | TypeScript | React hooks for FHIR resources — usePatient, useEncounter, etc. |
| 31 | `zs-pkg-ui-form-engine` | TypeScript | JSON-schema driven clinical form renderer (React Hook Form + Zod) |
| 32 | `zs-pkg-ui-offline-store` | TypeScript | Dexie.js offline FHIR resource store — IndexedDB with sync queue |
| 33 | `zs-pkg-ui-i18n` | TypeScript | i18next config — lazy-load language files for all supported locales |
| 34 | `zs-pkg-ui-charts` | TypeScript | Apache ECharts wrappers — clinical charts, vitals trends, BI dashboards |
| 35 | `zs-pkg-ui-maps` | TypeScript | OpenLayers wrappers — geo health data, facility maps, outbreak mapping |
| 36 | `zs-pkg-ui-auth` | TypeScript | SMART on FHIR 2.1 launch, OIDC session management for SPA |

---

## Layer 2 — Backend Microservices (Go)

### 2A. Clinical Services

| # | Repository | Port | Description |
|---|-----------|------|-------------|
| 37 | `zs-svc-patient` | 8001 | Patient registration, demographics, MPI (Master Patient Index) |
| 38 | `zs-svc-encounter` | 8002 | Encounter management — visits, episodes, care settings |
| 39 | `zs-svc-observation` | 8003 | Vitals, lab results, measurements (LOINC-coded FHIR Observations) |
| 40 | `zs-svc-medication` | 8004 | Medication requests, dispensing, administration records |
| 41 | `zs-svc-immunization` | 8005 | Immunization records, CVX coding, EPI program support |
| 42 | `zs-svc-condition` | 8006 | Diagnoses, problem lists, ICD-11 / SNOMED coding |
| 43 | `zs-svc-procedure` | 8007 | Procedures, surgical notes, clinical procedures |
| 44 | `zs-svc-allergy` | 8008 | AllergyIntolerance — allergens, reactions, severity |
| 45 | `zs-svc-appointment` | 8009 | Scheduling, slot management, referrals, waitlists |
| 46 | `zs-svc-document` | 8010 | Clinical documents, discharge summaries, FHIR DocumentReference |
| 47 | `zs-svc-diagnostic` | 8011 | Diagnostic reports, imaging orders, lab orders (DICOM integration) |
| 48 | `zs-svc-care-plan` | 8012 | CarePlan, CareTeam, Goals — chronic disease management |
| 49 | `zs-svc-consent` | 8013 | FHIR Consent resources — patient consent, data sharing agreements |
| 50 | `zs-svc-nutrition` | 8014 | NutritionOrder, nutritional assessments, MUAC, MIYCN tracking |
| 51 | `zs-svc-maternity` | 8015 | ANC/PNC workflows, delivery records, postnatal care |
| 52 | `zs-svc-child-health` | 8016 | Pediatric growth monitoring, EPI, IMCI, development milestones |
| 53 | `zs-svc-mental-health` | 8017 | Mental health assessments, MHPSS programs, PHQ-9, GAD-7 |
| 54 | `zs-svc-communicable-disease` | 8018 | CD surveillance, outbreak detection, EWARS integration |
| 55 | `zs-svc-laboratory` | 8019 | Lab order management, result ingestion, HL7 v2 Lab ADT |
| 56 | `zs-svc-pharmacy` | 8020 | Formulary management, dispensing, stock integration |
| 57 | `zs-svc-radiology` | 8021 | DICOM worklist, imaging orders, PACS integration |

### 2B. Public Health & Surveillance Services

| # | Repository | Port | Description |
|---|-----------|------|-------------|
| 58 | `zs-svc-ewars` | 8030 | Early Warning and Response System — outbreak alerts, threshold monitoring |
| 59 | `zs-svc-dhis2-bridge` | 8031 | DHIS2 data exchange — aggregate export, tracker import, metadata sync |
| 60 | `zs-svc-godata-bridge` | 8032 | Go.Data integration — contact tracing, case investigation |
| 61 | `zs-svc-surveillance` | 8033 | Disease surveillance, case counts, trend analysis, EWARS feeds |
| 62 | `zs-svc-population` | 8034 | Population registry, catchment areas, denominators for indicators |
| 63 | `zs-svc-indicator` | 8035 | Health indicator calculation engine — WHO/UNICEF KPI definitions |
| 64 | `zs-svc-reporting` | 8036 | Report generation engine — MOH reports, donor reports, HMIS exports |
| 65 | `zs-svc-analytics` | 8037 | Aggregate analytics, BI data pipelines, Grafana data source |

### 2C. Platform & Operational Services

| # | Repository | Port | Description |
|---|-----------|------|-------------|
| 66 | `zs-svc-auth` | 8050 | Auth service wrapping Keycloak — SMART on FHIR, user management |
| 67 | `zs-svc-notification` | 8051 | Multi-channel notifications — SMS, email, push, WhatsApp (Asynq queued) |
| 68 | `zs-svc-audit` | 8052 | Audit log aggregator — FHIR AuditEvent, HIPAA/GDPR event stream |
| 69 | `zs-svc-media` | 8053 | File/media management — photos, documents, Cloudflare R2 backed |
| 70 | `zs-svc-translation` | 8054 | Dynamic content translation service — UI strings, form labels |
| 71 | `zs-svc-terminology` | 8055 | Terminology server — ICD-11, SNOMED, LOINC, CIEL, CVX, RxNorm |
| 72 | `zs-svc-search` | 8056 | Typesense-backed search — patient name, clinical notes, facility |
| 73 | `zs-svc-geo` | 8057 | Geolocation service — facility registry, GPS tracking, health zones |
| 74 | `zs-svc-scheduler` | 8058 | Background job scheduler (Asynq) — report gen, sync, backups |
| 75 | `zs-svc-webhook` | 8059 | Webhook dispatcher — outbound FHIR R5 subscriptions, partner feeds |
| 76 | `zs-svc-import` | 8060 | Data import service — CSV, HL7 v2, legacy system migration |
| 77 | `zs-svc-export` | 8061 | Data export service — FHIR bulk export, CSV, PDF reports |
| 78 | `zs-svc-gateway` | 8080 | API Gateway config (Traefik) — routing, rate limiting, CORS |

### 2D. Business / ERP Services

| # | Repository | Port | Description |
|---|-----------|------|-------------|
| 79 | `zs-svc-hrm` | 8070 | Human Resource Management — staff registry, contracts, schedules |
| 80 | `zs-svc-finance` | 8071 | Financial management — budgets, expenditure, donor fund tracking |
| 81 | `zs-svc-accounting` | 8072 | Accounting — GL, cost centers, grant accounting |
| 82 | `zs-svc-inventory` | 8073 | Supply chain — medical stock, procurement, expiry tracking |
| 83 | `zs-svc-logistics` | 8074 | Logistics — cold chain, distribution, last-mile delivery tracking |
| 84 | `zs-svc-procurement` | 8075 | Procurement workflows — POs, vendor management, contracts |
| 85 | `zs-svc-crm` | 8076 | Community relations — beneficiary registry, referral management |
| 86 | `zs-svc-asset` | 8077 | Asset management — equipment, vehicles, maintenance logs |
| 87 | `zs-svc-project` | 8078 | Project management — grants, deliverables, M&E frameworks |

---

## Layer 3 — Frontend Microfrontends (React/Next.js)

### 3A. Clinical Microfrontends

| # | Repository | Domain | Description |
|---|-----------|--------|-------------|
| 88 | `zs-ui-patient-registration` | apps | Patient registration shell — demographics, ID, consent |
| 89 | `zs-ui-patient-profile` | apps | Patient profile — timeline, conditions, encounters |
| 90 | `zs-ui-encounter-form` | apps | Encounter form renderer — JSON-schema forms, clinical workflow |
| 91 | `zs-ui-vitals-entry` | apps | Vitals capture — BP, weight, height, MUAC, temp |
| 92 | `zs-ui-medication-management` | apps | Medication orders, dispensing, MAR |
| 93 | `zs-ui-appointment-scheduler` | apps | Appointment booking — calendar, slots, reminders |
| 94 | `zs-ui-lab-results` | apps | Lab result viewer — trend charts, reference ranges |
| 95 | `zs-ui-clinical-notes` | apps | Rich clinical notes — Tiptap editor, SOAP/POMR templates |
| 96 | `zs-ui-care-plan` | apps | Care plan builder — goals, tasks, team assignments |
| 97 | `zs-ui-maternity` | apps | Antenatal/Postnatal care — partograph, birth registry |
| 98 | `zs-ui-child-health` | apps | Child health — growth charts, EPI schedule, IMCI |
| 99 | `zs-ui-immunization` | apps | Vaccination records, EPI tracking, campaign management |
| 100 | `zs-ui-mental-health` | apps | Mental health — PHQ-9, GAD-7, MHPSS workflows |
| 101 | `zs-ui-communicable-disease` | apps | CD case investigation forms, outbreak response |
| 102 | `zs-ui-pharmacy` | apps | Pharmacy dispensing, formulary, stock alerts |
| 103 | `zs-ui-radiology` | apps | Imaging orders, DICOM viewer integration |

### 3B. Public Health & Surveillance Microfrontends

| # | Repository | Domain | Description |
|---|-----------|--------|-------------|
| 104 | `zs-ui-dashboard-clinical` | health | Clinical dashboard — facility-level KPIs, daily stats |
| 105 | `zs-ui-dashboard-public-health` | health | Public health dashboard — population indicators, program coverage |
| 106 | `zs-ui-dashboard-surveillance` | health | Disease surveillance — EWARS alerts, outbreak maps |
| 107 | `zs-ui-dashboard-analytics` | health | BI analytics — Grafana-embedded, customizable reports |
| 108 | `zs-ui-geo-mapping` | health | Geospatial health — facility maps, catchment zones, outbreaks |
| 109 | `zs-ui-indicator-tracker` | health | WHO/UNICEF indicator tracking, M&E dashboards |
| 110 | `zs-ui-report-builder` | health | Drag-and-drop report builder — MOH, donor, HMIS |

### 3C. Operational Microfrontends

| # | Repository | Domain | Description |
|---|-----------|--------|-------------|
| 111 | `zs-ui-admin-console` | ops | Platform admin — user management, org config, system settings |
| 112 | `zs-ui-form-builder` | forms | No-code form builder — drag-and-drop, JSON schema output |
| 113 | `zs-ui-workflow-builder` | ops | Clinical workflow designer — BPMN-lite, state machines |
| 114 | `zs-ui-terminology-browser` | ops | Terminology browser — ICD-11, SNOMED, LOINC, CIEL |
| 115 | `zs-ui-facility-registry` | ops | Facility management — health posts, hospitals, clinics |
| 116 | `zs-ui-user-management` | ops | User/role management — RBAC, team assignments |
| 117 | `zs-ui-audit-viewer` | ops | Audit log explorer — HIPAA-compliant access history |
| 118 | `zs-ui-notification-center` | ops | Notification management — channels, templates, schedules |
| 119 | `zs-ui-import-wizard` | ops | Data import wizard — CSV upload, field mapping, validation |

### 3D. Business / ERP Microfrontends

| # | Repository | Domain | Description |
|---|-----------|--------|-------------|
| 120 | `zs-ui-hrm` | ops | HR dashboard — staff profiles, leave, payroll, rosters |
| 121 | `zs-ui-inventory` | ops | Stock management — medical supplies, procurement, expiry |
| 122 | `zs-ui-finance` | ops | Finance — budgets, expenditure, donor reporting |
| 123 | `zs-ui-project-management` | ops | Grant/project management — milestones, M&E, donor reports |
| 124 | `zs-ui-crm` | ops | Community CRM — beneficiary lists, referrals, field visits |
| 125 | `zs-ui-logistics` | ops | Logistics & supply chain — cold chain, distribution, tracking |

### 3E. Shell Applications (Composition Layer)

| # | Repository | Domain | Description |
|---|-----------|--------|-------------|
| 126 | `zs-ui-shell-clinical` | apps | Clinical app shell — composes clinical microfrontends, routing |
| 127 | `zs-ui-shell-ops` | ops | Operations app shell — HR, finance, logistics, project shells |
| 128 | `zs-ui-shell-public` | health | Public health shell — surveillance, dashboards, maps |
| 129 | `zs-ui-portal-patient` | health | Patient-facing portal — records, appointments, messages |
| 130 | `zs-ui-landing` | — | Public website — zarishsphere.com landing pages, docs site |

---

## Layer 4 — Mobile Applications (Flutter)

| # | Repository | Platform | Description |
|---|-----------|----------|-------------|
| 131 | `zs-mobile-clinic` | Android/iOS | Primary clinical mobile app — offline-first EMR, PowerSync |
| 132 | `zs-mobile-community` | Android/iOS | Community health worker app — household visits, surveys, referrals |
| 133 | `zs-mobile-ewars` | Android/iOS | EWARS field reporting — outbreak alerts, tally sheets |
| 134 | `zs-mobile-supervisor` | Android/iOS | Supervisor dashboard — staff performance, facility monitoring |
| 135 | `zs-mobile-patient` | Android/iOS | Patient-facing app — appointments, records, health education |
| 136 | `zs-pkg-flutter-fhir` | Dart | Shared Flutter FHIR client, models, offline sync (PowerSync) |
| 137 | `zs-pkg-flutter-ui` | Dart | Shared Flutter component library — design system for mobile |

---

## Layer 5 — Desktop Applications (Tauri)

| # | Repository | Platform | Description |
|---|-----------|----------|-------------|
| 138 | `zs-desktop-clinic` | Win/Mac/Linux | Desktop clinical app — embeds Go FHIR binary, fully offline |
| 139 | `zs-desktop-admin` | Win/Mac/Linux | Platform admin desktop — org setup, bulk import, system config |

---

## Layer 6 — Data as Code (Terminologies, Concepts, Forms, Metadata)

### 6A. Clinical Content (Forms & Workflows)

| # | Repository | Description |
|---|-----------|-------------|
| 140 | `zs-content-forms-core` | Core clinical forms — registration, triage, vitals (JSON Schema) |
| 141 | `zs-content-forms-maternity` | ANC, PNC, delivery, postnatal forms (JSON Schema) |
| 142 | `zs-content-forms-child-health` | Pediatric forms — growth, EPI, IMCI, development (JSON Schema) |
| 143 | `zs-content-forms-nutrition` | MUAC, MIYCN, SAM/MAM assessment forms (JSON Schema) |
| 144 | `zs-content-forms-mental-health` | MHPSS forms — PHQ-9, GAD-7, psychosocial screening (JSON Schema) |
| 145 | `zs-content-forms-cd` | Communicable disease forms — TB, malaria, cholera, COVID (JSON Schema) |
| 146 | `zs-content-forms-ncd` | Non-communicable disease — HTN, DM, asthma, cancer screening |
| 147 | `zs-content-forms-emergency` | Emergency/trauma forms — triage, resuscitation, mass casualty |
| 148 | `zs-content-forms-laboratory` | Lab order and result forms — chemistry, hematology, microbiology |
| 149 | `zs-content-forms-pharmacy` | Medication order, dispensing, counseling forms |
| 150 | `zs-content-forms-community` | CHW forms — household surveys, WASH, GBV screening |
| 151 | `zs-content-forms-ops` | Operational forms — HR, logistics, procurement, finance |
| 152 | `zs-content-workflows` | Clinical workflow definitions — state machines, decision trees |
| 153 | `zs-content-protocols` | Clinical protocols — treatment algorithms, clinical decision support |

### 6B. Terminology & Concepts (Data as Code)

| # | Repository | Description |
|---|-----------|-------------|
| 154 | `zs-data-icd11` | ICD-11 (2025-01) — PostgreSQL seed, Go client, annual update pipeline |
| 155 | `zs-data-snomed` | SNOMED CT (July 2025) — RF2 loader, GIN-indexed PostgreSQL |
| 156 | `zs-data-loinc` | LOINC 2.78 — CSV loader, lab code mappings, panel definitions |
| 157 | `zs-data-ciel` | OpenMRS CIEL (2025-02) — concept dictionary, mappings, update pipeline |
| 158 | `zs-data-rxnorm` | RxNorm — NLM API client, medication name normalization |
| 159 | `zs-data-cvx` | CVX vaccine codes — CDC flat file loader, EPI program mappings |
| 160 | `zs-data-facility-registry` | Global health facility registry — FHIR Location resources by country |
| 161 | `zs-data-concept-maps` | Cross-terminology concept maps — ICD-10↔ICD-11, SNOMED↔CIEL |
| 162 | `zs-data-value-sets` | FHIR ValueSets — curated sets for ZarishSphere clinical domains |
| 163 | `zs-data-fhir-profiles` | FHIR Implementation Guide — ZarishSphere IG, StructureDefinitions |
| 164 | `zs-data-indicators` | WHO/UNICEF indicator definitions — FHIR Measure resources |
| 165 | `zs-data-translations` | Multilingual translations — EN, BN, MY, UR, HI, TH (JSON/CSV) |

---

## Layer 7 — Infrastructure as Code

### 7A. Core Infrastructure

| # | Repository | Description |
|---|-----------|-------------|
| 166 | `zs-iac-platform` | Core platform IaC — shared OpenTofu modules, Kubernetes base configs |
| 167 | `zs-iac-networking` | Networking — Traefik, Cilium, Cloudflare, DNS, TLS, CDN |
| 168 | `zs-iac-storage` | Storage — PostgreSQL, TimescaleDB, Valkey, NATS, Typesense configs |
| 169 | `zs-iac-observability` | Observability stack — Prometheus, Grafana, Loki, Tempo, Alertmanager |
| 170 | `zs-iac-security` | Security — HashiCorp Vault, Keycloak, network policies, RBAC |
| 171 | `zs-iac-registry` | Container registry — GHCR, image signing, Trivy scanning |
| 172 | `zs-iac-gitops` | ArgoCD config — ApplicationSets, app-of-apps pattern |
| 173 | `zs-iac-helm-charts` | Helm charts — all ZarishSphere services, shared chart library |
| 174 | `zs-iac-backup` | Backup automation — PostgreSQL, Valkey, R2 object storage |

### 7B. Country Infrastructure (per-country state)

| # | Repository | Country | Description |
|---|-----------|---------|-------------|
| 175 | `zs-infra-bgd` | 🇧🇩 Bangladesh | BGD environment — OpenTofu state, K8s manifests, site configs |
| 176 | `zs-infra-ind` | 🇮🇳 India | IND environment — OpenTofu state, K8s manifests, site configs |
| 177 | `zs-infra-mmr` | 🇲🇲 Myanmar | MMR environment — OpenTofu state, K8s manifests, site configs |
| 178 | `zs-infra-pak` | 🇵🇰 Pakistan | PAK environment — OpenTofu state, K8s manifests, site configs |
| 179 | `zs-infra-tha` | 🇹🇭 Thailand | THA environment — OpenTofu state, K8s manifests, site configs |

### 7C. Environment Templates

| # | Repository | Description |
|---|-----------|-------------|
| 180 | `zs-iac-template-country` | 🏗️ One-click country environment template — fork to onboard new country |
| 181 | `zs-iac-template-site` | 🏗️ Site-level deployment template — fork to add a health facility |
| 182 | `zs-iac-dev-environment` | Local development environment — Docker Compose, devcontainer configs |

---

## Layer 8 — Distribution Layer (Distros)

| # | Repository | Description |
|---|-----------|-------------|
| 183 | `zs-distro-core` | Core distro — global defaults, base FHIR profiles, core forms |
| 184 | `zs-distro-bgd` | Bangladesh distro — BGD forms, DHIS2 config, MOH terminology |
| 185 | `zs-distro-bgd-cxb` | Cox's Bazar distro — Rohingya refugee response, camp health programs |
| 186 | `zs-distro-ind` | India distro — national health ID, ABDM integration, IN forms |
| 187 | `zs-distro-mmr` | Myanmar distro — MMR forms, cross-border health, conflict settings |
| 188 | `zs-distro-pak` | Pakistan distro — PKI integration, Urdu forms, national codes |
| 189 | `zs-distro-tha` | Thailand distro — THA forms, national code sets, MoPH integration |
| 190 | `zs-distro-humanitarian` | Humanitarian distro — UNHCR, SPHERE standards, camp settings |
| 191 | `zs-distro-refugee-response` | Refugee response distro — biometric ID, camp registration, mVAM |

---

## Layer 9 — CI/CD, Agents & Automation

| # | Repository | Description |
|---|-----------|-------------|
| 192 | `zs-agent-platform-bot` | Main GitHub App — org-wide automation, RFC state machine, labeler |
| 193 | `zs-agent-code-review` | AI code review bot — FHIR compliance checks, Go lint, security scan |
| 194 | `zs-agent-content-validator` | Clinical content validator — JSON form validation, concept lookup |
| 195 | `zs-agent-dependency-updater` | Renovate bot config — dependency updates across all repos |
| 196 | `zs-agent-docs-generator` | Auto-generates API docs from OpenAPI specs, publishes to docs site |
| 197 | `zs-agent-release-manager` | Automated release notes, changelog, versioning across repos |
| 198 | `zs-agent-security-scanner` | Trivy + CodeQL + GitGuardian — org-wide security scanning |
| 199 | `zs-agent-smoke-tester` | Post-deploy smoke tests — FHIR endpoint health checks, critical paths |
| 200 | `zs-ops-github-actions` | Shared GitHub Actions — reusable workflows for all repositories |

---

## Layer 10 — Integration & Interoperability

| # | Repository | Description |
|---|-----------|-------------|
| 201 | `zs-int-dhis2` | DHIS2 integration — metadata sync, aggregate reporting, tracker |
| 202 | `zs-int-openmrs` | OpenMRS integration — patient sync, concept mapping, forms bridge |
| 203 | `zs-int-openimis` | OpenIMIS integration — health insurance, claims, beneficiary registry |
| 204 | `zs-int-godata` | Go.Data integration — contact tracing, case investigation export |
| 205 | `zs-int-hl7v2` | HL7 v2.9 adapter — ADT, ORU, ORM messages from legacy systems |
| 206 | `zs-int-dicom` | DICOM integration — PACS worklist, imaging order bridge |
| 207 | `zs-int-lims` | LIMS integration — lab order exchange, result ingestion |
| 208 | `zs-int-abdm` | ABDM (India) — Ayushman Bharat Digital Mission integration |
| 209 | `zs-int-sms-gateway` | SMS gateway adapters — Twilio, Africa's Talking, local operators |
| 210 | `zs-int-who-icd11` | WHO ICD-11 API client — code lookup, hierarchy traversal |

---

## Summary Statistics

| Layer | Count | Description |
|-------|-------|-------------|
| 0 — Governance | 10 | Docs, RFCs, ADRs, org-level files |
| 1 — Core Platform | 28 | FHIR engine + Go/TS shared libraries |
| 2 — Backend Services | 51 | Go microservices (clinical, PH, ops, ERP) |
| 3 — Frontend MFEs | 43 | React microfrontends + shell apps |
| 4 — Mobile | 7 | Flutter apps + shared packages |
| 5 — Desktop | 2 | Tauri apps |
| 6 — Data as Code | 26 | Forms, terminologies, FHIR IGs |
| 7 — Infrastructure | 17 | OpenTofu, Helm, GitOps, country infra |
| 8 — Distros | 9 | Country/program distribution layers |
| 9 — Agents & CI/CD | 9 | GitHub bots, actions, automation |
| 10 — Integrations | 10 | DHIS2, HL7v2, DICOM, partner systems |
| **TOTAL** | **212** | **Repositories** |

---

> This catalog is governed by `zs-docs-platform`. All additions require a PR against this file following RFC-0001 Naming Standards.
