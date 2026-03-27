# ZarishSphere Platform Architecture

> **Document Class:** ARCHITECTURE | **Version:** 1.0.0 | **ADR:** ADR-0001
> **Owner:** Technical Architecture Group | **Governance:** RFC-0001

---

## 1. Architectural Philosophy

> "If it is not documented, it does not exist. If it exists, it must be automatable."

ZarishSphere is architected on five inviolable principles:

1. **FHIR-Native First** — Every clinical data model is a FHIR R5 resource. No proprietary schemas.
2. **Go at the Core** — The entire backend and FHIR engine is Go. No Java. No .NET. No external processes.
3. **Offline Before Online** — Every feature must work without network. Sync is a bonus, not a dependency.
4. **Documentation as Code** — Architecture is code. Configs are code. Forms are code. Nothing is "tribal knowledge."
5. **Zero Lock-in** — If the vendor disappears tomorrow, ZarishSphere continues operating.

---

## 2. System Architecture Diagram

```
┌──────────────── CLIENTS ────────────────┐
│                                         │
│  Browser       Mobile       Desktop     │
│  (Next.js)   (Flutter)    (Tauri+Go)    │
│                                         │
└────────────────────┬────────────────────┘
                     │ HTTPS / WebSocket
                     ▼
┌──────────── API GATEWAY (Traefik 3.3) ──────────────┐
│  TLS termination · Rate limiting · Auth headers     │
│  Cloudflare Workers (edge) → Traefik (cluster)      │
└──────────────────────┬──────────────────────────────┘
                       │
        ┌──────────────┼──────────────┐
        ▼              ▼              ▼
  ┌───────────┐  ┌───────────┐  ┌───────────┐
  │ FHIR R5   │  │ Clinical  │  │  Public   │
  │  Engine   │  │ Services  │  │  Health   │
  │(zs-core-  │  │(zs-svc-*) │  │(zs-svc-  │
  │ fhir-*)   │  │           │  │  ewars,   │
  └─────┬─────┘  └─────┬─────┘  │ dhis2,   │
        │               │        │ godata)  │
        │               │        └────┬─────┘
        └───────────────┼─────────────┘
                        │
        ┌───────────────┼───────────────┐
        ▼               ▼               ▼
  ┌──────────┐   ┌──────────┐   ┌──────────┐
  │PostgreSQL│   │  NATS    │   │ Valkey   │
  │18.3+JSONB│   │JetStream │   │  Cache   │
  │TimescaleDB│  │(Events)  │   │(Sessions)│
  └──────────┘   └──────────┘   └──────────┘
        │
  ┌─────┴──────┐
  │ Typesense  │
  │  (Search)  │
  └────────────┘

Auth Layer:
  Keycloak 26.1 → SMART on FHIR 2.1 → OIDC 1.0 → OAuth 2.1

Observability:
  Prometheus → Grafana (metrics)
  Loki → Grafana (logs)
  Tempo → Grafana (traces)
  OpenTelemetry 1.40 (instrumentation)

GitOps Control Plane:
  GitHub → Argo CD → Kubernetes 1.35 → All services
```

---

## 3. FHIR Engine Architecture

### 3.1 Core Design

The FHIR engine (`zs-core-fhir-engine`) is a pure Go HTTP server that implements the FHIR R5 REST API. It is the most critical component of the platform.

```
zs-core-fhir-engine/
├── cmd/
│   └── server/          # main.go — entry point
├── internal/
│   ├── api/             # chi router, handlers, middleware
│   ├── fhir/            # FHIR resource operations (CRUD, search, history)
│   ├── search/          # FHIR search parameter implementation
│   ├── validation/      # FHIR R5 validation + profile validation
│   ├── subscription/    # FHIR R5 topic-based subscriptions (NATS)
│   ├── terminology/     # ICD-11, SNOMED, LOINC, CIEL clients
│   ├── auth/            # SMART on FHIR, JWT validation
│   ├── storage/         # PostgreSQL JSONB storage layer (pgx v5)
│   ├── cache/           # Valkey hot-resource cache
│   └── audit/           # AuditEvent generation (zerolog)
├── pkg/
│   └── fhirpath/        # FHIRPath 2.0 evaluator
├── migrations/          # golang-migrate SQL migrations
├── config/              # viper config (YAML + env)
└── deploy/              # Helm chart, K8s manifests
```

### 3.2 FHIR REST API Endpoints

```
GET    /fhir/R5/{ResourceType}           # Search
POST   /fhir/R5/{ResourceType}           # Create
GET    /fhir/R5/{ResourceType}/{id}      # Read
PUT    /fhir/R5/{ResourceType}/{id}      # Update
DELETE /fhir/R5/{ResourceType}/{id}      # Delete
GET    /fhir/R5/{ResourceType}/{id}/_history   # History
POST   /fhir/R5/                         # Batch/Transaction bundle
GET    /fhir/R5/metadata                 # CapabilityStatement
POST   /fhir/R5/$validate                # Validate resource
GET    /fhir/R5/ValueSet/$expand         # Terminology expansion
POST   /fhir/R5/CodeSystem/$lookup       # Code lookup
```

### 3.3 PostgreSQL FHIR Storage Schema

```sql
-- Core FHIR resource table
CREATE TABLE fhir_resources (
    id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    resource_type TEXT NOT NULL,
    fhir_id       TEXT NOT NULL,
    version_id    INTEGER NOT NULL DEFAULT 1,
    resource      JSONB NOT NULL,              -- Full FHIR resource JSON
    tenant_id     TEXT NOT NULL,               -- Multi-tenancy
    created_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    updated_at    TIMESTAMPTZ NOT NULL DEFAULT NOW(),
    deleted_at    TIMESTAMPTZ,
    UNIQUE(resource_type, fhir_id, tenant_id)
);

-- GIN index for FHIR search (O(log n) JSONB search)
CREATE INDEX idx_fhir_resources_resource ON fhir_resources USING GIN (resource);
CREATE INDEX idx_fhir_resources_type ON fhir_resources (resource_type, tenant_id);

-- FHIR history table
CREATE TABLE fhir_history (
    id            UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    resource_id   UUID REFERENCES fhir_resources(id),
    version_id    INTEGER NOT NULL,
    resource      JSONB NOT NULL,
    operation     TEXT NOT NULL, -- create, update, delete
    recorded_at   TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- FHIR search parameters (extracted for fast lookup)
CREATE TABLE fhir_search_params (
    resource_id   UUID REFERENCES fhir_resources(id) ON DELETE CASCADE,
    param_name    TEXT NOT NULL,
    param_type    TEXT NOT NULL, -- string, token, date, reference, quantity
    param_value   TEXT,
    param_date    TIMESTAMPTZ,
    param_number  NUMERIC
);
CREATE INDEX idx_search_params ON fhir_search_params (param_name, param_value);
```

### 3.4 Offline Architecture

```
Offline Mode (Flutter Mobile / Tauri Desktop):

  ┌─────────────────────────────────┐
  │  Local FHIR Server (embedded)   │
  │  Go binary → SQLite/JSONB       │
  │  Same REST API as cloud FHIR    │
  └────────────┬────────────────────┘
               │ PowerSync / sync queue
               ▼ (when connected)
  ┌─────────────────────────────────┐
  │  Cloud PostgreSQL               │
  │  Conflict resolution: last-write│
  │  + clinical override rules      │
  └─────────────────────────────────┘
```

---

## 4. Microservice Architecture

### 4.1 Service Communication Patterns

```
Synchronous (REST):
  Client → API Gateway → Service A → FHIR Engine (reads/writes)

Asynchronous (Events):
  Service A → NATS JetStream → Service B, C, D (fan-out)
  Example: Patient created → Notification + Audit + Analytics

FHIR Subscriptions:
  Client registers Topic subscription → 
  FHIR engine emits events → NATS → Webhook dispatcher → Client
```

### 4.2 Service Template Structure

Every microservice follows this canonical structure:

```
zs-svc-{name}/
├── cmd/
│   └── server/
│       └── main.go
├── internal/
│   ├── api/
│   │   ├── router.go          # chi router setup
│   │   ├── middleware.go      # auth, logging, tracing
│   │   └── handlers/
│   │       └── {resource}.go  # HTTP handlers
│   ├── service/
│   │   └── {name}.go         # Business logic
│   ├── repository/
│   │   └── {name}.go         # PostgreSQL queries (pgx v5)
│   ├── model/
│   │   └── {name}.go         # Domain models
│   └── event/
│       └── {name}.go         # NATS event publishers/subscribers
├── migrations/
│   └── *.sql                 # golang-migrate files
├── config/
│   ├── config.go
│   └── config.yaml
├── deploy/
│   ├── Dockerfile
│   ├── helm/
│   └── k8s/
├── docs/
│   └── openapi.yaml          # OpenAPI 3.1 spec
├── tests/
│   ├── unit/
│   └── integration/          # testcontainers-go
├── Makefile
└── README.md
```

### 4.3 Authentication Flow (SMART on FHIR 2.1)

```
1. Client → Keycloak: Request access token (OAuth 2.1)
   - scopes: patient/*.read, patient/Observation.write, etc.
   - launch context: patient, encounter, user

2. Keycloak → Client: JWT access token
   - sub: user_id
   - azp: client_id  
   - scope: patient/Patient.read user/Practitioner.read
   - fhirUser: Practitioner/123

3. Client → API Gateway: Request with Bearer token

4. API Gateway → Auth middleware: Validate JWT
   - go-oidc v3 validates against Keycloak JWKS endpoint
   - golang-jwt parses SMART scopes
   - RBAC enforced per resource type

5. Service: Process request with user context
   - All DB queries scoped to tenant_id + user permissions
   - AuditEvent generated for every access
```

---

## 5. Frontend Architecture

### 5.1 Micro-Frontend Composition

```
Shell Application (zs-ui-shell-clinical)
├── Navigation + Auth context
├── Module federation host
└── Lazy-loads microfrontends:
    ├── zs-ui-patient-registration   (Remote 1)
    ├── zs-ui-encounter-form         (Remote 2)
    ├── zs-ui-vitals-entry           (Remote 3)
    └── zs-ui-clinical-notes         (Remote 4)
```

Each microfrontend is independently deployable, independently versioned, and independently releasable. Module federation (Webpack/Vite) enables runtime composition.

### 5.2 Offline Strategy

```
Online:                    Offline:
FHIR API ──────────────→  Dexie.js (IndexedDB)
    ↑                           │
    └─── Background Sync ←──────┘
         (Service Worker)
         
Conflict resolution:
  - Server wins for administrative data
  - Clinical provider wins for clinical data
  - Timestamps + version vectors track conflicts
  - Unresolved conflicts queued for manual review
```

### 5.3 Form Engine Architecture

Clinical forms are JSON Schema documents stored in `zs-content-forms-*` repos. The form engine (`zs-pkg-ui-form-engine`) renders them dynamically:

```json
{
  "$schema": "https://zarishsphere.com/schema/form/v1",
  "id": "zs-form-anc-visit-01",
  "title": "ANC Visit Form",
  "version": "1.2.0",
  "locale": "en",
  "fhirResource": "Observation",
  "sections": [
    {
      "id": "vitals",
      "title": "Vital Signs",
      "fields": [
        {
          "id": "blood_pressure_systolic",
          "type": "number",
          "label": "Systolic BP (mmHg)",
          "fhirPath": "Observation.component[code.coding.code='8480-6'].valueQuantity.value",
          "validation": { "min": 60, "max": 250, "required": true },
          "loincCode": "8480-6"
        }
      ]
    }
  ]
}
```

---

## 6. Data Architecture

### 6.1 Database Layout

```
PostgreSQL 18.3 (single instance — the only database ZarishSphere needs)
├── schema: fhir           # FHIR R5 resources (JSONB)
├── schema: clinical       # Normalized clinical tables (fast queries)
├── schema: auth           # Keycloak schema (managed by Keycloak)
├── schema: terminology    # ICD-11, SNOMED, LOINC, CIEL, RxNorm, CVX
├── schema: audit          # FHIR AuditEvents (time-partitioned)
├── schema: analytics      # Pre-aggregated analytics views
├── schema: jobs           # Asynq job queues
└── schema: search         # Typesense sync queue

TimescaleDB (PostgreSQL extension):
└── hypertables: vitals_timeline, lab_trends, indicator_timeseries
```

### 6.2 Multi-Tenancy Model

```
Tenant hierarchy:
  Organization (e.g., CPI International)
    └── Program (e.g., BGD Health Program)
          └── Site (e.g., Camp 1W Health Post)
                └── Patient (scoped to Site)

tenant_id format: {org_code}:{program_code}:{site_code}
Example:          cpi:bgd-health:camp-1w-hp

All database tables include tenant_id.
Row-level security (PostgreSQL RLS) enforces tenant isolation.
```

### 6.3 Terminology Architecture

```
Terminology lookup flow:
  1. Check Valkey cache (TTL: 24h)
  2. Check local PostgreSQL table
  3. Call external API (fallback, with circuit breaker)
  4. Store in PostgreSQL + Valkey

Sources:
  ICD-11  → WHO REST API (id.who.int) + local PostgreSQL mirror
  SNOMED  → RF2 bulk download → PostgreSQL GIN indexed
  LOINC   → CSV download → PostgreSQL
  CIEL    → OpenMRS wiki download → PostgreSQL
  RxNorm  → NLM REST API → PostgreSQL local cache
  CVX     → CDC flat file → PostgreSQL
```

---

## 7. Infrastructure Architecture

### 7.1 Kubernetes Cluster Layout

```
Namespace: zs-core        → FHIR engine, shared services
Namespace: zs-clinical    → Clinical microservices  
Namespace: zs-public-health → PH and surveillance services
Namespace: zs-ops         → Auth, notifications, audit
Namespace: zs-data        → PostgreSQL, TimescaleDB, NATS, Valkey
Namespace: zs-search      → Typesense
Namespace: zs-obs         → Prometheus, Grafana, Loki, Tempo
Namespace: zs-gateway     → Traefik, Cloudflare tunnel
Namespace: zs-gitops      → Argo CD
Namespace: zs-auth        → Keycloak, Vault
```

### 7.2 GitOps Flow

```
Developer pushes code
       ↓
GitHub Actions CI:
  - go test ./...
  - golangci-lint
  - trivy vulnerability scan
  - docker build + push to GHCR
  - helm package
       ↓
PR merged to main
       ↓
Argo CD detects change in zs-iac-gitops
       ↓
Argo CD syncs Kubernetes cluster
       ↓
Rolling deployment (zero-downtime)
       ↓
Smoke tests (zs-agent-smoke-tester)
       ↓
Notification to Slack/email
```

### 7.3 Cloudflare Edge Architecture

```
DNS: zarishsphere.com → Cloudflare
  
Cloudflare Workers (edge, free tier):
  - Rate limiting
  - Geo-routing (route BGD traffic to BGD cluster)
  - Auth token validation (HMAC check)
  - Bot protection

Cloudflare R2 (10GB free):
  - Patient documents
  - Report PDFs
  - Profile photos
  - Database backups (encrypted)

Cloudflare Pages (free):
  - docs.zarishsphere.com → static Docusaurus site
```

---

## 8. Security Architecture

### 8.1 Defense in Depth

```
Layer 1: Cloudflare (DDoS, WAF, rate limiting)
Layer 2: Traefik (TLS, auth headers, rate limits)
Layer 3: SMART on FHIR scopes (fine-grained access control)
Layer 4: FHIR resource-level RBAC (per resource type/ID)
Layer 5: PostgreSQL RLS (row-level tenant isolation)
Layer 6: Field-level encryption (PHI at rest)
Layer 7: AuditEvent logging (every access logged)
```

### 8.2 Secrets Management

```
HashiCorp Vault (self-hosted, free OSS):
  - Database credentials (dynamic secrets, auto-rotated)
  - API keys (Keycloak, NATS, external services)
  - TLS certificates
  - Encryption keys (field-level encryption)

Vault Agent Injector → Kubernetes pods receive secrets as env vars
No secrets in Git. Ever. Enforced by GitGuardian scanning.
```

### 8.3 Compliance Controls

| Control | Implementation |
|---------|---------------|
| GDPR Right to Erasure | Patient delete workflow → AuditEvent → FHIR soft delete → scrub pipeline |
| HIPAA Audit Trail | Every FHIR access → AuditEvent → immutable log (Loki) |
| Data Minimization | Role-based field masking (PHI masked for non-clinical users) |
| Consent Management | FHIR Consent resources → enforced by auth middleware |
| Encryption at Rest | AES-256 field encryption via Vault Transit |
| Encryption in Transit | TLS 1.3 everywhere (Cloudflare + Traefik) |
| RBAC | Keycloak roles + SMART scopes + FHIR compartment rules |

---

## 9. Observability Architecture

```
Metrics:   Go services → Prometheus (via otelhttp) → Grafana dashboards
Logs:      Go services → zerolog JSON → Loki → Grafana explore
Traces:    Go services → OpenTelemetry 1.40 → Tempo → Grafana trace view
Alerts:    Prometheus AlertManager → email / Slack / PagerDuty (free tier)
Uptime:    Grafana Synthetic Monitoring → external endpoint checks
```

### Key Dashboards

| Dashboard | Metrics Tracked |
|-----------|----------------|
| FHIR Engine Health | Request rate, latency (p50/p95/p99), error rate |
| Patient Activity | Registrations, encounters per day, active users |
| Clinical Operations | Appointments, lab turnaround, medication fills |
| Public Health | Case counts, outbreak alerts, indicator trends |
| Infrastructure | CPU, memory, DB connections, K8s pod health |
| Security | Auth failures, unusual access patterns, API abuse |

---

## 10. Architectural Decision Records (Key Decisions)

| ADR | Decision | Rationale |
|-----|----------|-----------|
| ADR-0001 | Go as primary backend language | Performance, concurrency, single binary, Raspberry Pi support |
| ADR-0002 | FHIR R5 as primary clinical data model | Current standard, topic subscriptions, future-proof |
| ADR-0003 | PostgreSQL as only database | JSONB for FHIR, GIN search, TimescaleDB extension, battle-tested |
| ADR-0004 | NATS JetStream for messaging | 20MB RAM, single binary, persistent, FHIR subscription support |
| ADR-0005 | Valkey over Redis | Linux Foundation fork, fully open source, drop-in compatible |
| ADR-0006 | OpenTofu over Terraform | Zero licensing cost, community fork, HashiCorp BSL concern |
| ADR-0007 | Argo CD for GitOps | CNCF graduated, UI, multi-cluster, app-of-apps |
| ADR-0008 | Cilium over Istio | eBPF-native, 60% less overhead, CNCF graduated |
| ADR-0009 | Carbon DS as design system | IBM health-focused, WCAG 2.2 AA, actively maintained |
| ADR-0010 | Cloudflare as CDN/DNS/Edge | Free tier covers ZarishSphere's full edge needs |
| ADR-0011 | Microfrontend architecture | Independent deployability, team autonomy, country customization |
| ADR-0012 | PowerSync for mobile offline | Self-hosted, SQLite-to-PostgreSQL sync, clinical-safe |

---

> **See also:** `BLUEPRINT.md` for platform overview, `CATALOGS.md` for all repositories.
> Changes to this document require ADR + RFC + 2 owner approvals.
