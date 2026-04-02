# Repository Naming Standards

> **RFC:** RFC-0001 | **Status:** Accepted | **Date:** 2026-03-16
> **ADR:** ADR-0001 (naming convention)

---

## The Convention

All ZarishSphere repositories follow:

```
zs-{layer}-{module}[-{submodule}]
```

**Rules:**
- Always lowercase
- Hyphens only (no underscores, no dots, no spaces)
- Maximum 50 characters total
- The `zs-` prefix is mandatory
- Layer must be one of the defined layers below

---

## Defined Layers

| Layer | Prefix | What lives here |
|-------|--------|----------------|
| Core platform | `zs-core-` | FHIR engine, FHIRPath evaluator |
| Shared packages | `zs-pkg-` | Shared Go libraries, TypeScript packages |
| Backend services | `zs-svc-` | Go microservices |
| Frontend | `zs-ui-` | React microfrontends (Next.js 16.2) |
| Mobile | `zs-mobile-` | Flutter 3.41.2 apps |
| Desktop | `zs-desktop-` | Tauri 2.3 apps |
| Clinical content | `zs-content-` | JSON Schema forms, protocols |
| Data as Code | `zs-data-` | Terminologies, translations, FHIR IGs |
| Infrastructure | `zs-iac-` | OpenTofu, Helm, K8s |
| Country infra | `zs-infra-` | Per-country OpenTofu state |
| Distribution | `zs-distro-` | Country configurations |
| Agents | `zs-agent-` | GitHub automation bots |
| Integrations | `zs-int-` | External system adapters |
| Documentation | `zs-docs-` | Governance, standards, runbooks |
| Operations | `zs-ops-` | Shared GitHub Actions, bots |

---

## Examples

```
zs-core-fhir-engine     ← FHIR R5 server
zs-svc-patient          ← Patient registration microservice
zs-ui-patient-profile   ← Patient profile microfrontend
zs-content-forms-core   ← Core clinical forms
zs-data-icd11           ← ICD-11 terminology
zs-iac-platform         ← Core infrastructure IaC
zs-distro-bgd           ← Bangladesh distribution
zs-int-dhis2            ← DHIS2 integration adapter
zs-docs-adr             ← Architecture Decision Records
```

## What Is Not Allowed

```
zarishsphere-patient-service   ← Missing zs- prefix
zs_patient_service             ← Underscores not allowed
zs-patient                     ← Missing layer prefix
zs-svc-PatientService          ← No capitals
zs-svc-patient_registration    ← Underscores in module
ZS-SVC-PATIENT                 ← No uppercase
```

---

## Automation Depends on This Convention

The CI/CD system infers:
- Dockerfile build commands from layer prefix
- Helm chart location from layer prefix
- Which CI workflow to use based on layer
- Which owner to assign for review based on layer

Deviating from the naming convention **breaks automation**.
