# Commit Message Convention

> ZarishSphere uses [Conventional Commits](https://www.conventionalcommits.org/) v1.0.0.
> This is enforced by commitlint in CI — non-conforming commits will block your PR.

---

## Format

```
<type>(<scope>): <short description>

[optional body — explain WHY, not WHAT]

[optional footer]
Closes #<issue-number>
```

## Types

| Type | Use for | Triggers |
|------|---------|---------|
| `feat` | New feature | Minor version bump |
| `fix` | Bug fix | Patch version bump |
| `docs` | Documentation only | No version bump |
| `refactor` | Code restructure, no behaviour change | No version bump |
| `test` | Tests only | No version bump |
| `chore` | Maintenance, dependencies | No version bump |
| `ci` | CI/CD pipeline | No version bump |
| `content` | Clinical forms, protocols, terminology | Patch version bump |
| `perf` | Performance improvement | Patch version bump |
| `style` | Formatting, no logic change | No version bump |

## Scopes

Use the repository or module name as scope:

```
feat(patient): ...         # zs-svc-patient
fix(fhir-engine): ...     # zs-core-fhir-engine
docs(camm): ...           # zs-docs-camm
content(forms): ...       # zs-content-forms-*
chore(deps): ...           # dependency updates
ci(go-test): ...           # CI workflow changes
```

## Breaking Changes

Add `!` after the type to indicate a breaking change:

```
feat!(patient)!: change Patient identifier schema
```

This triggers a major version bump and requires 2 owner approvals.

## Examples

```
feat(patient): add UNHCR refugee ID to patient identifiers

UNHCR registration number is now supported as a patient identifier type.
This enables refugee camp programs to link ZarishSphere records to
UNHCR registration data.

Closes #142

---

fix(fhir-engine): correct Content-Type header for FHIR search bundles

Search responses were returning application/json instead of
application/fhir+json, breaking strict FHIR clients.

Closes #156

---

docs(contributing): add no-coder guide for platform owners

---

content(forms): add PHQ-9 mental health screening form

Adds PHQ-9 form with:
- All 9 items with LOINC codes
- Automatic severity scoring
- EN, BN, MY translations
- FHIR Observation mapping

Closes #201

---

chore(deps): update pgx to v5.7.2
```
