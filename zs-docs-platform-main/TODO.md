# ZarishSphere Platform — Master TODO & Roadmap

> **Document Class:** TODO | **Version:** 1.0.0 | **Updated:** 2026-03-15
> **Governance:** All items become GitHub Issues in their respective repositories.
> **Priority:** P0 = Blocking | P1 = Critical | P2 = Important | P3 = Nice-to-have

---

## Phase 0 — Platform Bootstrap (NOW → Month 1)

Everything in Phase 0 must be done before any code is written.

### 0.1 GitHub Organization Setup

- [ ] **P0** Create GitHub organization `zarishsphere` — confirm all 4 owners added
- [ ] **P0** Create `.github` repository — org-level PR/issue templates, code of conduct
- [ ] **P0** Set organization defaults: branch protection, required reviews (2 owners), signed commits
- [ ] **P0** Install GitHub Apps: Renovate, Dependabot, CodeRabbit (free for OSS), GitGuardian
- [ ] **P0** Configure GHCR (GitHub Container Registry) for public image hosting
- [ ] **P0** Create org-level GitHub Actions secrets (vault tokens, deploy keys)
- [ ] **P0** Configure Cloudflare DNS for all subdomains
- [ ] **P0** Set up Cloudflare Email Routing → zarishsphere@gmail.com

### 0.2 Core Documentation Repositories

- [ ] **P0** Create `zs-docs-platform` — write CONTRIBUTING.md, RFC process, naming standards
- [ ] **P0** Create `zs-docs-rfc` — RFC-0001 (Naming Standards), RFC-0002 (Repo Structure)
- [ ] **P0** Create `zs-docs-adr` — ADR-0001 through ADR-0012 (from ARCHITECTURE.md)
- [ ] **P0** Create `zs-docs-standards` — FHIR profile policy, terminology policy
- [ ] **P0** Create `zs-docs-security` — threat model, GDPR policy, HIPAA controls
- [ ] **P1** Create `zs-docs-camm` — Country Adoption Maturity Model document
- [ ] **P1** Create `zs-docs-runbooks` — initial incident, deploy, backup runbooks
- [ ] **P2** Create `zs-docs-donor` — donor report templates, impact framework

### 0.3 Shared GitHub Actions

- [ ] **P0** Create `zs-ops-github-actions` — reusable CI/CD workflows:
  - `go-test.yml` — Go test + lint + security scan
  - `go-build.yml` — Go build + GHCR push
  - `helm-lint.yml` — Helm chart lint + validate
  - `fhir-validate.yml` — FHIR resource/profile validation
  - `form-validate.yml` — JSON Schema form validation
  - `openapi-lint.yml` — OpenAPI 3.1 spec lint
  - `trivy-scan.yml` — Container vulnerability scan
  - `release.yml` — Semantic release + changelog

---

## Phase 1 — Core Infrastructure (Month 1–2)

### 1.1 Development Environment

- [ ] **P0** Create `zs-iac-dev-environment` — Docker Compose for local dev:
  - PostgreSQL 18.3 + TimescaleDB
  - NATS 2.10.24 + JetStream
  - Valkey 8.0
  - Typesense 28.0
  - Keycloak 26.1
  - Traefik 3.3
  - Grafana + Prometheus + Loki + Tempo
- [ ] **P0** Write devcontainer config (VS Code + GitHub Codespaces)
- [ ] **P0** Write Makefile targets: `make dev`, `make test`, `make build`, `make clean`

### 1.2 Shared Go Libraries

- [ ] **P0** Create `zs-pkg-go-fhir` — fhir-toolbox-go wrapper, FHIR builders
- [ ] **P0** Create `zs-pkg-go-db` — pgx v5 pool wrapper, JSONB helpers
- [ ] **P0** Create `zs-pkg-go-auth` — OIDC middleware, SMART scope validator
- [ ] **P0** Create `zs-pkg-go-audit` — FHIR AuditEvent builder, zerolog adapter
- [ ] **P0** Create `zs-pkg-go-messaging` — NATS JetStream client, event types
- [ ] **P1** Create `zs-pkg-go-cache` — Valkey client, FHIR caching patterns
- [ ] **P1** Create `zs-pkg-go-telemetry` — OpenTelemetry setup, Prometheus exporter
- [ ] **P1** Create `zs-pkg-go-config` — Viper config loader, secret resolution
- [ ] **P1** Create `zs-pkg-go-migration` — golang-migrate wrapper, migration runner
- [ ] **P2** Create `zs-pkg-go-testing` — testcontainers fixtures, FHIR test data
- [ ] **P2** Create `zs-pkg-go-i18n` — multilingual string resolution
- [ ] **P2** Create `zs-pkg-go-crypto` — AES encryption, Vault Transit client

### 1.3 FHIR Engine

- [ ] **P0** Create `zs-core-fhir-engine`:
  - [ ] PostgreSQL FHIR storage schema + migrations
  - [ ] FHIR R5 CRUD operations (Create, Read, Update, Delete)
  - [ ] FHIR search parameters (string, token, date, reference, quantity)
  - [ ] FHIR history endpoint
  - [ ] FHIR batch/transaction bundle processing
  - [ ] FHIR CapabilityStatement endpoint
  - [ ] SMART on FHIR 2.1 auth integration
  - [ ] Multi-tenancy (tenant_id scoping, PostgreSQL RLS)
  - [ ] AuditEvent logging for all operations
  - [ ] OpenTelemetry instrumentation
  - [ ] Prometheus metrics
  - [ ] Dockerfile + Helm chart
  - [ ] Integration tests (testcontainers-go)
- [ ] **P0** FHIR engine runs on Raspberry Pi 5 (validate ARM64 build)
- [ ] **P1** Create `zs-core-fhir-r4-bridge` — R4↔R5 resource translation
- [ ] **P1** Create `zs-core-fhir-subscriptions` — NATS-backed topic subscriptions
- [ ] **P2** Create `zs-core-fhir-validator` — standalone validator service
- [ ] **P2** Create `zs-core-fhir-cds-hooks` — CDS Hooks 2.0 service
- [ ] **P2** Create `zs-core-fhirpath` — FHIRPath 2.0 evaluator library

### 1.4 Infrastructure IaC

- [ ] **P1** Create `zs-iac-platform` — base OpenTofu modules
- [ ] **P1** Create `zs-iac-networking` — Traefik + Cilium + Cloudflare configs
- [ ] **P1** Create `zs-iac-storage` — PostgreSQL, NATS, Valkey Helm configs
- [ ] **P1** Create `zs-iac-observability` — Prometheus/Grafana/Loki/Tempo stack
- [ ] **P1** Create `zs-iac-security` — Vault, Keycloak, network policies
- [ ] **P1** Create `zs-iac-gitops` — Argo CD app-of-apps configuration
- [ ] **P1** Create `zs-iac-helm-charts` — shared Helm chart library
- [ ] **P2** Create `zs-iac-backup` — PostgreSQL backup automation to R2
- [ ] **P2** Create `zs-iac-template-country` — one-click country template

---

## Phase 2 — Core Clinical Services (Month 2–4)

### 2.1 Foundation Clinical Services (Go)

- [ ] **P0** Create `zs-svc-patient` — patient registration, MPI:
  - FHIR Patient resource CRUD
  - Biographic data management
  - Duplicate detection (Jaro-Winkler similarity)
  - Patient search (Typesense integration)
  - Multi-ID support (national ID, refugee ID, UNHCR, custom)
- [ ] **P0** Create `zs-svc-encounter` — encounter management:
  - FHIR Encounter CRUD
  - Visit types (OPD, IPD, emergency, home visit, CHW)
  - Care setting management
  - Encounter status workflow
- [ ] **P0** Create `zs-svc-observation` — vitals and measurements:
  - FHIR Observation CRUD
  - LOINC-coded measurements
  - Vitals: BP, HR, RR, temp, weight, height, MUAC, SpO2
  - Trend analytics (TimescaleDB)
- [ ] **P1** Create `zs-svc-condition` — diagnosis, problem list (ICD-11/SNOMED)
- [ ] **P1** Create `zs-svc-medication` — medication requests, dispensing
- [ ] **P1** Create `zs-svc-immunization` — vaccination records, EPI
- [ ] **P1** Create `zs-svc-appointment` — scheduling, slots, reminders
- [ ] **P2** Create `zs-svc-allergy` — allergy/intolerance records
- [ ] **P2** Create `zs-svc-procedure` — procedures, surgical notes
- [ ] **P2** Create `zs-svc-document` — clinical documents, discharge summaries
- [ ] **P2** Create `zs-svc-care-plan` — care plans, goals, care teams
- [ ] **P2** Create `zs-svc-consent` — FHIR Consent management
- [ ] **P3** Create `zs-svc-diagnostic` — diagnostic reports, DICOM orders
- [ ] **P3** Create `zs-svc-laboratory` — lab order management, HL7 v2 Lab
- [ ] **P3** Create `zs-svc-pharmacy` — formulary, dispensing, stock

### 2.2 Platform Services

- [ ] **P0** Create `zs-svc-auth` — Keycloak wrapper, SMART on FHIR
- [ ] **P0** Create `zs-svc-terminology` — ICD-11, SNOMED, LOINC, CIEL server
- [ ] **P1** Create `zs-svc-notification` — SMS, email, push notifications (Asynq)
- [ ] **P1** Create `zs-svc-audit` — AuditEvent aggregator
- [ ] **P1** Create `zs-svc-search` — Typesense patient/clinical search
- [ ] **P2** Create `zs-svc-media` — file upload/download, Cloudflare R2
- [ ] **P2** Create `zs-svc-translation` — dynamic content translation
- [ ] **P2** Create `zs-svc-geo` — facility registry, geospatial data
- [ ] **P2** Create `zs-svc-scheduler` — Asynq background job runner
- [ ] **P2** Create `zs-svc-reporting` — report generation engine
- [ ] **P3** Create `zs-svc-import` — CSV/HL7 data import service
- [ ] **P3** Create `zs-svc-export` — FHIR bulk export, CSV/PDF

---

## Phase 3 — Frontend & Mobile (Month 3–5)

### 3.1 Shared Frontend Libraries

- [ ] **P0** Create `zs-pkg-ui-design-system` — Carbon DS extensions, ZS theme
- [ ] **P0** Create `zs-pkg-ui-fhir-hooks` — React hooks for FHIR resources
- [ ] **P0** Create `zs-pkg-ui-form-engine` — JSON schema form renderer
- [ ] **P1** Create `zs-pkg-ui-offline-store` — Dexie.js FHIR offline storage
- [ ] **P1** Create `zs-pkg-ui-auth` — SMART on FHIR SPA auth
- [ ] **P1** Create `zs-pkg-ui-i18n` — i18next config, language files
- [ ] **P2** Create `zs-pkg-ui-charts` — ECharts clinical chart wrappers
- [ ] **P2** Create `zs-pkg-ui-maps` — OpenLayers health map wrappers

### 3.2 Clinical Web Microfrontends

- [ ] **P0** Create `zs-ui-shell-clinical` — main clinical app shell
- [ ] **P0** Create `zs-ui-patient-registration` — demographics, ID, consent
- [ ] **P0** Create `zs-ui-encounter-form` — form renderer for clinical encounters
- [ ] **P0** Create `zs-ui-vitals-entry` — vitals capture form
- [ ] **P1** Create `zs-ui-patient-profile` — patient timeline, history
- [ ] **P1** Create `zs-ui-appointment-scheduler` — booking, calendar
- [ ] **P1** Create `zs-ui-medication-management` — medication orders, MAR
- [ ] **P1** Create `zs-ui-clinical-notes` — Tiptap rich text clinical notes
- [ ] **P2** Create `zs-ui-lab-results` — lab result viewer, trend charts
- [ ] **P2** Create `zs-ui-immunization` — vaccination records, EPI schedule
- [ ] **P2** Create `zs-ui-maternity` — ANC/PNC forms, partograph
- [ ] **P2** Create `zs-ui-child-health` — growth charts, IMCI
- [ ] **P2** Create `zs-ui-dashboard-clinical` — facility KPIs
- [ ] **P2** Create `zs-ui-admin-console` — user management, org settings
- [ ] **P2** Create `zs-ui-form-builder` — no-code form designer
- [ ] **P3** Create `zs-ui-report-builder` — drag-and-drop report builder
- [ ] **P3** Create `zs-ui-terminology-browser` — code browser/search
- [ ] **P3** Create `zs-ui-geo-mapping` — facility and outbreak maps

### 3.3 Mobile (Flutter)

- [ ] **P1** Create `zs-pkg-flutter-fhir` — Flutter FHIR client, PowerSync
- [ ] **P1** Create `zs-pkg-flutter-ui` — shared Flutter component library
- [ ] **P1** Create `zs-mobile-clinic` — primary offline clinical mobile app
- [ ] **P2** Create `zs-mobile-community` — CHW app, household surveys
- [ ] **P3** Create `zs-mobile-ewars` — outbreak field reporting
- [ ] **P3** Create `zs-mobile-supervisor` — supervisor monitoring

### 3.4 Desktop (Tauri)

- [ ] **P2** Create `zs-desktop-clinic` — offline desktop clinic app (embedded FHIR)
- [ ] **P3** Create `zs-desktop-admin` — admin desktop app

---

## Phase 4 — Clinical Content & Data (Month 2–6, parallel)

### 4.1 Terminology Data

- [ ] **P0** Create `zs-data-icd11` — ICD-11 loader, Go client, update pipeline
- [ ] **P0** Create `zs-data-loinc` — LOINC 2.78 CSV loader
- [ ] **P1** Create `zs-data-snomed` — SNOMED CT RF2 bulk loader
- [ ] **P1** Create `zs-data-ciel` — OpenMRS CIEL concept dictionary
- [ ] **P1** Create `zs-data-cvx` — CVX vaccine codes, CDC flat file
- [ ] **P2** Create `zs-data-rxnorm` — RxNorm NLM API client, local cache
- [ ] **P2** Create `zs-data-value-sets` — curated FHIR ValueSets
- [ ] **P2** Create `zs-data-concept-maps` — cross-terminology mappings
- [ ] **P2** Create `zs-data-fhir-profiles` — ZarishSphere FHIR IG
- [ ] **P2** Create `zs-data-facility-registry` — health facility FHIR Locations
- [ ] **P2** Create `zs-data-indicators` — WHO/UNICEF FHIR Measure definitions
- [ ] **P2** Create `zs-data-translations` — multilingual content JSON/CSV

### 4.2 Clinical Forms (JSON Schema)

- [ ] **P0** Create `zs-content-forms-core` — registration, triage, vitals forms
- [ ] **P1** Create `zs-content-forms-maternity` — ANC, PNC, delivery forms
- [ ] **P1** Create `zs-content-forms-child-health` — pediatric, EPI, IMCI
- [ ] **P1** Create `zs-content-forms-nutrition` — MUAC, CMAM, MIYCN
- [ ] **P2** Create `zs-content-forms-mental-health` — PHQ-9, GAD-7, MHPSS
- [ ] **P2** Create `zs-content-forms-cd` — TB, malaria, cholera, COVID
- [ ] **P2** Create `zs-content-forms-community` — CHW, household, WASH, GBV
- [ ] **P2** Create `zs-content-forms-laboratory` — lab orders, results
- [ ] **P2** Create `zs-content-forms-pharmacy` — medication, dispensing
- [ ] **P2** Create `zs-content-forms-ncd` — HTN, DM, asthma
- [ ] **P2** Create `zs-content-forms-emergency` — triage, trauma, MCI
- [ ] **P3** Create `zs-content-forms-ops` — HR, logistics, finance forms
- [ ] **P3** Create `zs-content-workflows` — clinical workflow definitions
- [ ] **P3** Create `zs-content-protocols` — clinical decision support protocols

---

## Phase 5 — Public Health & Integration (Month 4–8)

### 5.1 Public Health Services

- [ ] **P1** Create `zs-svc-ewars` — EWARS outbreak alerting
- [ ] **P1** Create `zs-svc-surveillance` — disease surveillance engine
- [ ] **P2** Create `zs-svc-indicator` — health indicator calculation
- [ ] **P2** Create `zs-svc-population` — population registry, denominators
- [ ] **P2** Create `zs-svc-analytics` — aggregate analytics, BI pipeline

### 5.2 Integration Adapters

- [ ] **P1** Create `zs-svc-dhis2-bridge` — DHIS2 bidirectional data exchange
- [ ] **P2** Create `zs-int-dhis2` — DHIS2 connector library
- [ ] **P2** Create `zs-int-hl7v2` — HL7 v2.9 adapter (ADT, Lab)
- [ ] **P2** Create `zs-int-openmrs` — OpenMRS FHIR bridge
- [ ] **P2** Create `zs-svc-godata-bridge` — Go.Data contact tracing export
- [ ] **P3** Create `zs-int-dicom` — DICOM PACS integration
- [ ] **P3** Create `zs-int-lims` — LIMS lab result ingestion
- [ ] **P3** Create `zs-int-openimis` — OpenIMIS health insurance bridge
- [ ] **P3** Create `zs-int-abdm` — India ABDM integration
- [ ] **P3** Create `zs-int-sms-gateway` — SMS gateway adapters

---

## Phase 6 — ERP Business Modules (Month 5–10)

- [ ] **P2** Create `zs-svc-hrm` — staff registry, contracts, schedules
- [ ] **P2** Create `zs-svc-inventory` — medical stock, procurement, expiry
- [ ] **P3** Create `zs-svc-finance` — budgets, expenditure, grants
- [ ] **P3** Create `zs-svc-accounting` — GL, cost centers, grant accounting
- [ ] **P3** Create `zs-svc-logistics` — cold chain, distribution, tracking
- [ ] **P3** Create `zs-svc-procurement` — POs, vendors, contracts
- [ ] **P3** Create `zs-svc-crm` — community CRM, beneficiaries, referrals
- [ ] **P3** Create `zs-svc-asset` — equipment, vehicles, maintenance
- [ ] **P3** Create `zs-svc-project` — grants, deliverables, M&E

---

## Phase 7 — Country Distros (Month 6–12)

### Bangladesh (BGD) — Priority 1

- [ ] **P0** Create `zs-distro-bgd` — Bangladesh national distro
- [ ] **P0** Create `zs-distro-bgd-cxb` — Cox's Bazar / Rohingya program
- [ ] **P0** Create `zs-infra-bgd` — BGD infrastructure state (OpenTofu)
- [ ] **P1** BGD-specific forms: camp registration, UNHCR ID integration
- [ ] **P1** BGD-specific indicators: SPHERE, UNHCR health KPIs
- [ ] **P1** BGD DHIS2 integration — DGHS national HMIS
- [ ] **P2** Bengali (BN) full UI translation

### Other Countries

- [ ] **P2** Create `zs-distro-ind` + `zs-infra-ind` — India
- [ ] **P2** Create `zs-distro-mmr` + `zs-infra-mmr` — Myanmar
- [ ] **P3** Create `zs-distro-pak` + `zs-infra-pak` — Pakistan
- [ ] **P3** Create `zs-distro-tha` + `zs-infra-tha` — Thailand

---

## Phase 8 — Automation Agents (Month 3–ongoing)

- [ ] **P0** Create `zs-agent-platform-bot` — GitHub App for org automation
- [ ] **P1** Create `zs-agent-dependency-updater` — Renovate config
- [ ] **P1** Create `zs-agent-security-scanner` — Trivy + CodeQL org-wide
- [ ] **P2** Create `zs-agent-content-validator` — form/concept validation bot
- [ ] **P2** Create `zs-agent-docs-generator` — OpenAPI → docs auto-publish
- [ ] **P2** Create `zs-agent-release-manager` — semantic versioning + changelogs
- [ ] **P2** Create `zs-agent-code-review` — FHIR compliance code review
- [ ] **P3** Create `zs-agent-smoke-tester` — post-deploy health checks

---

## Ongoing / Always

- [ ] Update CATALOGS.md when any new repo is created
- [ ] Write RFC for every architectural decision
- [ ] Write ADR for every accepted RFC that has architectural impact
- [ ] All services must have OpenAPI 3.1 specs
- [ ] All services must have integration tests (testcontainers-go)
- [ ] All repos must pass Trivy security scan before merge
- [ ] No secrets in any repository (GitGuardian enforced)
- [ ] All forms must validate against JSON Schema 2020-12
- [ ] All FHIR resources must validate against FHIR R5 profiles

---

> This TODO is the master roadmap. Individual repository TODOs link back here.
> Convert each item to a GitHub Issue in the appropriate repository as work begins.
