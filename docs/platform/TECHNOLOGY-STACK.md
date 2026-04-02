# ZarishSphere Technology Stack

> **Version:** 2.0.0 | **Updated:** 2026-03-24
> **Status:** Canonical — all versions verified against official release sources on 2026-03-24

---

## Version Freeze Policy

All versions listed here are **frozen** for the current platform generation. Upgrades require:
1. A Renovate PR (automatic, weekly)
2. CI passes
3. For major upgrades: an ADR update

Do not change versions in code without updating this document.

---

## Backend Stack

| Component | Version | Docker Image | Notes |
|-----------|---------|--------------|-------|
| **Go** | **1.26.1** | `golang:1.26.1-alpine` | Primary backend language |
| **PostgreSQL** | **18.3** | `timescale/timescaledb:2.25-pg18` | With TimescaleDB 2.25 |
| **TimescaleDB** | **2.25** | included above | Time-series for vitals |
| **NATS JetStream** | **2.12.5** | `nats:2.12.5-alpine` | Messaging |
| **Valkey** | **9.0.3** | `valkey/valkey:9.0.3-alpine` | Cache & sessions |
| **Typesense** | **30.1** | `typesense/typesense:30.1` | Search engine |
| **Keycloak** | **26.5.6** | `quay.io/keycloak/keycloak:26.5.6` | Auth / SMART on FHIR |

### Go Library Versions (go.mod)

| Library | Version | Purpose |
|---------|---------|---------|
| `github.com/go-chi/chi/v5` | v5.2.2 | HTTP router |
| `github.com/jackc/pgx/v5` | v5.7.x | PostgreSQL driver |
| `github.com/rs/zerolog` | latest | Structured logging |
| `github.com/spf13/viper` | latest | Config management |
| `github.com/coreos/go-oidc/v3` | v3 | OIDC / SMART on FHIR |
| `github.com/golang-jwt/jwt/v5` | v5 | JWT parsing |
| `github.com/nats-io/nats.go` | v1.39+ | NATS JetStream |
| `github.com/valkey-io/valkey-go` | latest | Valkey client |
| `go.opentelemetry.io/otel` | 1.35+ | Observability |
| `github.com/prometheus/client_golang` | latest | Metrics |
| `github.com/golang-migrate/migrate/v4` | v4 | DB migrations |
| `github.com/testcontainers/testcontainers-go` | latest | Integration tests |
| `github.com/stretchr/testify` | latest | Test assertions |
| `github.com/damedic/fhir-toolbox-go` | latest | FHIR R5 engine |
| `github.com/fastenhealth/gofhir-models` | latest | FHIR R5 Go types |

---

## Frontend Stack

| Component | Version | CDN / Package | Notes |
|-----------|---------|---------------|-------|
| **React** | **19.x** | `react@19` | UI framework |
| **Next.js** | **16.2** | `next@16.2` | Meta-framework |
| **TypeScript** | **6.0** | `typescript@6` | Strict mode |
| **Vite** | 6.x | `vite@6` | Build tool |
| **Carbon DS** | 11.x | `@carbon/react@11` | Design system |
| React Hook Form | 7.54+ | `react-hook-form` | Form state |
| Zod | 3.24+ | `zod@3` | Validation |
| Zustand | 5.0 | `zustand@5` | Global state |
| TanStack Query | v5 | `@tanstack/react-query@5` | Server state |
| Dexie.js | 4.0 | `dexie@4` | IndexedDB offline |
| Apache ECharts | 5.6+ | `echarts@5` | Clinical charts |
| OpenLayers | 10.3+ | `ol@10` | Maps |
| i18next | 24.x | `i18next@24` | Internationalisation |

---

## Mobile Stack

| Component | Version | Notes |
|-----------|---------|-------|
| **Flutter** | **3.41.2** | Cross-platform mobile |
| **Dart** | **3.11** | With Flutter 3.41 |
| Riverpod | 2.6+ | State management |
| PowerSync | 1.x | Offline sync to PostgreSQL |
| drift | 2.x | SQLite ORM |
| go_router | 13+ | Navigation |
| freezed | 2.x | Immutable models |
| dio | 5.x | HTTP client |
| flutter_appauth | 6+ | SMART on FHIR auth |

---

## Desktop Stack

| Component | Version | Notes |
|-----------|---------|-------|
| **Tauri** | **2.3** | Desktop wrapper |
| Rust | stable | Tauri native layer |

---

## Infrastructure Stack

| Component | Version | Docker Image / Binary | Notes |
|-----------|---------|----------------------|-------|
| **Docker** | **29.3** | — | Container runtime |
| **Kubernetes** | **1.35** | — | Orchestration |
| **Helm** | **3.17+** | — | K8s package manager |
| **OpenTofu** | **1.11.x** | `ghcr.io/opentofu/opentofu:1.11` | IaC |
| **Argo CD** | **3.3.x** | `quay.io/argoproj/argocd:v3.3` | GitOps |
| **Traefik** | **3.x** | `traefik:3` | API gateway |
| **Cilium** | 1.17+ | Helm install | eBPF networking |
| **Grafana** | 12.x | `grafana/grafana:12` | Dashboards |
| **Prometheus** | 3.0+ | `prom/prometheus:v3` | Metrics |
| **Loki** | 3.4+ | `grafana/loki:3.4` | Logs |
| **Tempo** | 2.7+ | `grafana/tempo:2.7` | Traces |
| **HashiCorp Vault** | 1.18+ | `hashicorp/vault:1.18` | Secrets |
| cert-manager | latest | Helm install | TLS automation |

---

## Free-Tier Services

| Service | Free Tier Used |
|---------|---------------|
| GitHub | Public repos, Actions (2000 min/month), Pages, GHCR |
| Cloudflare | DNS, CDN, SSL, Workers (100k/day), R2 (10GB), Pages |
| Vercel | Frontend deployments (OSS) |
| Oracle Cloud | 2× ARM VMs free forever (Ampere A1, 24 GB total RAM) |

---

## Version Conflicts — Resolved

The following conflicts existed across older documents. They are resolved here:

| Component | Old Conflicting Values | ✅ Canonical (March 2026) |
|-----------|----------------------|--------------------------|
| NATS | 2.10.24 vs 2.12.5 | **2.12.5** |
| Valkey | 8.0 vs 9.0.3 | **9.0.3** |
| Next.js | 15.3 vs 16.2 | **16.2** |
| TypeScript | 5.8 vs 6.0 | **6.0** |
| Flutter | 3.29 vs 3.41.2 | **3.41.2** |
| Dart | 3.7 vs 3.11 | **3.11** |
| OpenTofu | 1.9.1 vs 1.11.x | **1.11.x** |
| Argo CD | 2.14 vs 3.3.x | **3.3.x** |
| Keycloak | 26.1 vs 26.5.6 | **26.5.6** |
| Typesense | 28.0 vs 30.1 | **30.1** |

---

*This document is updated with every dependency upgrade. CI validates that go.mod and package.json match the versions listed here.*
