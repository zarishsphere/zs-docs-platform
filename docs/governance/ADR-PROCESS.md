# ADR Process & Guidelines

> **Version:** 1.0.0 | **Last Updated:** 2026-03-27 | **Governance:** RFC-0001

---

## What is an ADR?

An **ADR (Architectural Decision Record)** is a short document that records:

1. **Context** — What problem did we face?
2. **Decision** — What did we decide to do?
3. **Rationale** — Why did we choose this?
4. **Consequences** — What are the tradeoffs?

ADRs are **immutable records** of every significant architectural choice. They live forever in [zs-docs-adr](https://github.com/zarishsphere/zs-docs-adr/tree/main/docs).

---

## When is an ADR Created?

Every **ACCEPTED RFC** gets an ADR.

Some ADRs exist without RFCs (for decisions made early in project history).

---

## ADR Structure

```markdown
# ADR-XXXX: Title

**Status:** Accepted  
**Date:** 2026-03-27  
**Context:** What problem did we face?  
**Decision:** What did we decide?  
**Rationale:** Why this choice?  
**Consequences:** What are the tradeoffs?  

## Context

Describe the situation that prompted this decision.

## Decision

What decision did we make? Be specific and concrete.

## Rationale

Why is this the right choice?
- Benefit 1
- Benefit 2

Alternative considered: Option X
- Reason for rejection

## Consequences

### Positive
✅ Easier to X
✅ Better performance for Y

### Negative
❌ More complex setup
❌ Vendor lock-in if Z

### Migration Path (if applicable)
How do we upgrade from v1 to v2?

## Related ADRs

- ADR-0001: Something related
- ADR-0003: Something else

## Related RFCs

- RFC-0001: Something that triggered this
```

---

## Complete ADR Catalog (ADR-0001 to ADR-0014)

### ADR-0001: Go as Primary Backend Language

| Aspect | Decision |
|--------|----------|
| Status | Accepted |
| Context | Need single language for all backend services |
| Decision | Go 1.26.1 for all backend/services |
| Rationale | Single binary, Green Tea GC, ARM64, tiny memory footprint, concurrency |
| Consequences | Team learns Go; no Python/Node.js backends; all CLI tools in Go |

### ADR-0002: FHIR R5 as Primary Clinical Model

| Aspect | Decision |
|--------|----------|
| Status | Accepted |
| Context | Need open, standard clinical model |
| Decision | All clinical data as FHIR R5; no proprietary schemas |
| Rationale | Standard endorsed by WHO/UNICEF; topic subscriptions; profile-based validation |
| Consequences | Team learns FHIR; forms map to FHIR; reports export FHIR |

### ADR-0003: PostgreSQL 18.3 as Sole Database

| Aspect | Decision |
|--------|----------|
| Status | Accepted |
| Context | Need single, proven OLTP database |
| Decision | PostgreSQL 18.3 (+ TimescaleDB 2.26.0 for timeseries) |
| Rationale | Async I/O (3× read speed); JSONB for FHIR; uuidv7(); open source; ARM64 support |
| Consequences | No MongoDB, no DynamoDB; JSON queries via JSONB; schema migrations required |

### ADR-0004: NATS JetStream 2.12.5 for Messaging

| Aspect | Decision |
|--------|----------|
| Status | Accepted |
| Context | Need event broadcast across services |
| Decision | NATS JetStream 2.12.5 (not Kafka, RabbitMQ) |
| Rationale | 20MB RAM; single binary; clustering built-in; FHIR event types; no external schema registry |
| Consequences | Easy local dev; built-in JetStream persistence; team learns NATS paradigm |

### ADR-0005: Valkey 9.0.3 over Redis

| Aspect | Decision |
|--------|----------|
| Status | Accepted |
| Context | Need fast session/cache layer |
| Decision | Valkey 9.0.3 (not Redis) |
| Rationale | Linux Foundation maintained; atomic slot migration (no downtime); same API as Redis |
| Consequences | Can use Go `valkey-go` client; drop-in replacement if migrating from Redis |

### ADR-0006: OpenTofu 1.11.x over Terraform

| Aspect | Decision |
|--------|----------|
| Status | Accepted |
| Context | Need infrastructure as code |
| Decision | OpenTofu 1.11.x (open-source fork of Terraform) |
| Rationale | No corporate licensing; Linux Foundation governed; same HCL syntax; open module registry |
| Consequences | No Terraform Cloud lock-in; self-hosted state management required; community modules available |

### ADR-0007: Argo CD 3.3.x for GitOps

| Aspect | Decision |
|--------|----------|
| Status | Accepted |
| Context | Need declarative, version-controlled deployments |
| Decision | Argo CD 3.3.x (CNCF project) |
| Rationale | Git as source of truth; app-of-apps for multi-service deployments; Helm 3.17+ support; no external deploy pipeline |
| Consequences | Git branch = deployment target; all Infrastructure in GitHub; automated sync |

### ADR-0008: Cilium over Istio

| Aspect | Decision |
|--------|----------|
| Status | Accepted |
| Context | Need service mesh for Kubernetes |
| Decision | Cilium (eBPF-native) |
| Rationale | 60% less overhead than Istio; kernel-level networking; Tetra for policy |
| Consequences | Low operational overhead; runs on K8s 1.35+; no sidecar proxies |

### ADR-0009: Carbon DS 11.x as Design System

| Aspect | Decision |
|--------|----------|
| Status | Accepted |
| Context | Need professional, accessible UI components |
| Decision | Carbon Design System 11.x (IBM open source) |
| Rationale | WCAG 2.2 AA certified; healthcare-optimized; React 19.x support; no licensing costs |
| Consequences | All UI follows Carbon patterns; theme overridable for country distros |

### ADR-0010: Cloudflare as CDN/DNS/Edge

| Aspect | Decision |
|--------|----------|
| Status | Accepted |
| Context | Need global CDN, DNS, edge computing |
| Decision | Cloudflare (free tier covers all ZS needs) |
| Rationale | Free tier: unlimited domains, DDoS, WAF, Workers; email routing; Tunnel for private networks |
| Consequences | DNS managed by Cloudflare; edge functions for auth at edge; R2 for document storage |

### ADR-0011: Next.js 16.2 for Microfrontends

| Aspect | Decision |
|--------|----------|
| Status | Accepted |
| Context | Need scalable frontend architecture |
| Decision | Next.js 16.2 + Module Federation for microfrontends |
| Rationale | Turbopack default; streaming SSR; OpenTelemetry built-in; Vercel support |
| Consequences | Each UI repo = independent Node.js deployment; shared design system; unified auth layer |

### ADR-0012: PowerSync for Mobile Offline

| Aspect | Decision |
|--------|----------|
| Status | Accepted |
| Context | Mobile apps must work offline |
| Decision | PowerSync 1.x (SQLite sync engine) |
| Rationale | Self-hosted; SQLite → PostgreSQL 18.3 sync; conflict resolution; offline-first SDK |
| Consequences | Mobile DB mirrors server; bidirectional sync; conflict handling logic needed |

### ADR-0013: TypeScript 6.0 for Frontend

| Aspect | Decision |
|--------|----------|
| Status | Accepted |
| Context | Need type safety in frontend |
| Decision | TypeScript 6.0 (last JavaScript-based release) |
| Rationale | Strict mode; catches errors at build time; IDE support; stable for 2026+|
| Consequences | All JS must be TS; Zod for runtime validation; no `any` allowed |

### ADR-0014: Flutter 3.41.2 for Mobile

| Aspect | Decision |
|--------|----------|
| Status | Accepted |
| Context | Need cross-platform mobile (Android + iOS) |
| Decision | Flutter 3.41.2 + Dart 3.11 |
| Rationale | Single codebase; native performance; Riverpod state management; hot reload |
| Consequences | All mobile dev in Dart; PowerSync for offline; separate iOS/Android signing |

---

## How to Create an ADR

1. Number it sequentially (ADR-0015, ADR-0016, etc.)
2. Write in [zs-docs-adr/docs/ADR-XXXX.md](https://github.com/zarishsphere/zs-docs-adr/tree/main/docs)
3. Add to index: [zs-docs-adr/README.md](https://github.com/zarishsphere/zs-docs-adr)
4. Create PR to merge to `main`
5. Upon merge, it becomes canonical record

## Updating an ADR

ADRs are **immutable**. If a decision changes:

1. Create new ADR (ADR-0015) explaining the change
2. Mark old ADR as "Superseded by ADR-0015"
3. Include migration guide in new ADR

Example:

```markdown
# ADR-0015: Migrate from X to Y

**Status:** Accepted  
**Supersedes:** ADR-0005  

## Reason for Change

ADR-0005 chose Valkey, but after 6 months we discovered...

## Migration Path

1. Phase 1: Deploy Y alongside X
2. Phase 2: Dual-write to both
3. Phase 3: Migrate existing data
4. Phase 4: Stop writing to X
5. Phase 5: Decommission X
```

---

## FAQ

**Q: Can I challenge a decision recorded in an ADR?**
A: Yes! Open an issue in [zs-docs-adr](https://github.com/zarishsphere/zs-docs-adr/issues) to propose superseding the ADR via a new RFC.

**Q: Where are the ADRs stored?**
A: All ADRs live in [zs-docs-adr](https://github.com/zarishsphere/zs-docs-adr) repository. GitHub is the source of truth.

---

See also:
- [RFC Process](./RFC-PROCESS.md)
- [ADR Repository](https://github.com/zarishsphere/zs-docs-adr)
