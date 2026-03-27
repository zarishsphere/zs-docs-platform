# Naming Standards for ZarishSphere Repositories

> **Version:** 1.0.0 | **Last Updated:** 2026-03-27 | **Governance:** RFC-0001

---

## Canonical Pattern

All repositories must follow:

```
zs-{layer}-{module}[-{submodule}]
```

### Layers

| Layer   | Prefix         | Example                        |
| ------- | ------------- | ------------------------------ |
| core    | zs-core-      | zs-core-fhir-engine            |
| svc     | zs-svc-       | zs-svc-patient                 |
| ui      | zs-ui-        | zs-ui-patient-registration     |
| mobile  | zs-mobile-    | zs-mobile-clinic               |
| desktop | zs-desktop-   | zs-desktop-clinic              |
| pkg     | zs-pkg-       | zs-pkg-go-fhir                 |
| iac     | zs-iac-       | zs-iac-platform                |
| data    | zs-data-      | zs-data-icd11                  |
| content | zs-content-   | zs-content-forms-core           |
| docs    | zs-docs-      | zs-docs-platform               |
| agent   | zs-agent-     | zs-agent-platform-bot           |
| distro  | zs-distro-    | zs-distro-bgd                  |
| infra   | zs-infra-     | zs-infra-bgd                   |
| ops     | zs-ops-       | zs-ops-github-actions           |
| int     | zs-int-       | zs-int-dhis2                   |

### Module Naming

- Use **kebab-case** (lowercase, hyphens)
- Short, descriptive, domain-specific
- No underscores, no camelCase

### Examples

- `zs-svc-patient` — Patient service
- `zs-ui-encounter-form` — Encounter form UI
- `zs-content-forms-maternity` — Maternity forms
- `zs-docs-platform` — Platform documentation
- `zs-agent-code-review` — Code review bot

---

## Enforcement

- **Branch protection**: Only repos matching this pattern are allowed
- **Automation**: `zs-agent-platform-bot` checks PRs to CATALOGS.md
- **Audit**: Weekly report lists non-compliant repos

---

## See Also

- [RFC-0001: Naming Standards](../../zs-docs-rfc/accepted/RFC-0001-naming-standards.md)
- [CATALOGS.md](../../CATALOGS.md)
