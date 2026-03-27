# ZarishSphere Platform — Agent & Automation Specification

> **Document Class:** AGENT | **Version:** 1.0.0
> **Owner:** DevOps Architecture Group | **Governance:** RFC-0003

---

## Philosophy

ZarishSphere operates as a **no-code, no-server-management, fully automated GitHub organization**. The platform owner's role is to:

- ✅ Approve
- ✅ Review
- ✅ Decide

Everything else is done by agents, bots, templates, and automation. No terminal. No manual deploys. No manual config.

---

## Agent Catalog

### Agent 1: `zs-agent-platform-bot`

**Role:** The master GitHub App that orchestrates the entire ZarishSphere GitHub organization.

**Triggers:** Every repository event across the organization (PR, issue, push, comment, label, merge).

**Capabilities:**

| Feature | Description |
|---------|-------------|
| RFC State Machine | Opens RFC → Labels (draft/review/accepted/rejected) → Closes with decision |
| Auto-Labeler | Classifies PRs by files changed (backend/frontend/infra/docs/content) |
| PR Validator | Checks PR title format, linked issue, CHANGELOG entry, test coverage |
| Issue Triage | Labels new issues by type, assigns to milestone, pings owners |
| Merge Automation | Auto-merges Dependabot + Renovate PRs after CI passes (patch/minor) |
| Branch Protection | Enforces branch naming conventions across all repos |
| Repository Audit | Weekly report: stale PRs, missing README, missing OpenAPI specs |
| Onboarding Assistant | Welcomes new contributors, links CONTRIBUTING.md, assigns mentor |

**Stack:** Go + GitHub App SDK (`google/go-github`) + Webhook handlers

**Deployment:** Cloudflare Workers (free tier, 100k req/day) — no server needed.

**Repository:** `zs-agent-platform-bot`

---

### Agent 2: `zs-agent-code-review`

**Role:** AI-powered code review bot specializing in FHIR compliance, Go patterns, and security.

**Triggers:** PR opened, PR updated (new commits).

**Review Checklist:**

```
Go Code:
  ✓ No hardcoded credentials or secrets
  ✓ Error handling (errors.Is/As, no bare panic)
  ✓ Context propagation (ctx passed everywhere)
  ✓ Structured logging (zerolog, no fmt.Println)
  ✓ OpenTelemetry trace spans on external calls
  ✓ Unit tests for business logic
  ✓ Integration tests for repository layer

FHIR Code:
  ✓ Resources include meta.lastUpdated
  ✓ AuditEvent generated for PHI access
  ✓ Tenant isolation (tenant_id scoped queries)
  ✓ SMART scope checked before resource access
  ✓ R4 bridge consideration for partner compatibility

Security:
  ✓ No secrets committed (pre-check with GitGuardian)
  ✓ SQL queries parameterized (no string concatenation)
  ✓ Input validation on all HTTP handlers
  ✓ Rate limiting on public endpoints

Clinical Forms (JSON Schema):
  ✓ FHIR mapping field exists for every clinical field
  ✓ LOINC/SNOMED code present for coded fields
  ✓ Multilingual label keys exist in translation repo
  ✓ Required fields marked correctly
  ✓ Schema validates against JSON Schema 2020-12
```

**Stack:** Calls Claude API (Anthropic) + GitHub PR review API

**Repository:** `zs-agent-code-review`

---

### Agent 3: `zs-agent-content-validator`

**Role:** Validates all clinical content (forms, concepts, terminologies) before merge.

**Triggers:** PRs to `zs-content-forms-*`, `zs-data-*` repositories.

**Validations:**

```yaml
# For JSON Schema forms:
- schema_valid: JSON validates against ZS Form Schema v1
- fhir_mappings: every clinical field has fhirPath defined
- code_valid: LOINC/SNOMED codes exist in local terminology DB
- translations_exist: label keys exist in zs-data-translations
- no_pii_defaults: no real patient data in default values

# For FHIR profiles (StructureDefinitions):
- profile_valid: validates against FHIR R5 meta-schema
- no_breaking_changes: diff against previous version (semver)
- snapshot_generated: snapshot element exists

# For concept maps:
- source_codes_valid: source codes exist in source system
- target_codes_valid: target codes exist in target system
- equivalence_set: all equivalence values are valid FHIR codes

# For terminology data (CSV/JSON):
- required_columns: all required columns present
- no_duplicates: no duplicate codes in same code system
- encoding: file is valid UTF-8
```

**Stack:** Go CLI + GitHub Actions

**Repository:** `zs-agent-content-validator`

---

### Agent 4: `zs-agent-dependency-updater`

**Role:** Keeps all dependencies current across all 212 repositories automatically.

**Tool:** Renovate Bot (free for open source — `renovatebot/renovate`)

**Configuration Strategy:**

```json
{
  "$schema": "https://docs.renovatebot.com/renovate-schema.json",
  "extends": ["config:base"],
  "schedule": ["every weekend"],
  "prCreationSchedule": ["after 9am on Monday"],
  "automerge": true,
  "automergeType": "pr",
  "automergeStrategy": "merge",
  "packageRules": [
    {
      "matchUpdateTypes": ["patch", "minor"],
      "matchPackagePatterns": ["*"],
      "automerge": true
    },
    {
      "matchUpdateTypes": ["major"],
      "automerge": false,
      "labels": ["major-update", "needs-review"],
      "assignees": ["arwa-zarish", "code-and-brain"]
    },
    {
      "matchPackageNames": [
        "github.com/damedic/fhir-toolbox-go",
        "github.com/fastenhealth/gofhir-models"
      ],
      "automerge": false,
      "labels": ["fhir-update", "breaking-possible"],
      "reviewers": ["code-and-brain"]
    }
  ]
}
```

**Repository:** `zs-agent-dependency-updater` (hosts Renovate config)

---

### Agent 5: `zs-agent-security-scanner`

**Role:** Org-wide automated security scanning — containers, code, secrets, dependencies.

**Tools Used (all free for OSS):**

| Tool | Scans | Frequency |
|------|-------|-----------|
| **Trivy** | Container images, Go deps, Kubernetes manifests | Every PR + nightly |
| **CodeQL** | Go source code SAST | Every PR |
| **GitGuardian** | Secret detection (all commits) | Every commit |
| **osv-scanner** | Open source vulnerability DB | Every PR |
| **gitleaks** | Secrets in git history | Every PR |
| **semgrep** | Custom security rules | Every PR |

**GitHub Actions Workflow:**

```yaml
name: Security Scan
on:
  push:
    branches: [main]
  pull_request:

jobs:
  trivy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: aquasecurity/trivy-action@master
        with:
          scan-type: 'fs'
          severity: 'CRITICAL,HIGH'
          exit-code: '1'  # Fail on CRITICAL/HIGH

  codeql:
    uses: github/codeql-action/analyze@v3
    with:
      languages: go

  gitleaks:
    uses: gitleaks/gitleaks-action@v2
```

**Repository:** `zs-agent-security-scanner`

---

### Agent 6: `zs-agent-docs-generator`

**Role:** Auto-generates and publishes documentation from code.

**What it generates:**

| Source | Output | Published To |
|--------|--------|-------------|
| OpenAPI 3.1 YAML files | API reference docs (Redoc) | docs.zarishsphere.com/api |
| AsyncAPI 3.0 YAML files | Event catalog | docs.zarishsphere.com/events |
| Go source (godoc) | Go package docs | docs.zarishsphere.com/go |
| JSON Schema forms | Form schema browser | forms.zarishsphere.com |
| FHIR StructureDefinitions | FHIR IG site (SUSHI/IG Publisher) | docs.zarishsphere.com/fhir |
| CATALOGS.md | Repository browser | docs.zarishsphere.com/repos |
| ADR files | ADR log site | docs.zarishsphere.com/adr |

**Stack:** GitHub Actions + Docusaurus 3 (free, OSS) + Cloudflare Pages (free)

**Repository:** `zs-agent-docs-generator`

---

### Agent 7: `zs-agent-release-manager`

**Role:** Fully automated semantic versioning, changelog generation, and GitHub Releases.

**How it works:**

```
Commit message convention (Conventional Commits):
  feat: add patient search → minor version bump
  fix: correct LOINC code mapping → patch version bump
  feat!: change FHIR resource schema → major version bump
  BREAKING CHANGE: ... → major version bump

On merge to main:
  1. semantic-release analyzes commits since last release
  2. Determines version bump (major/minor/patch)
  3. Generates CHANGELOG.md entry
  4. Creates GitHub Release with release notes
  5. Tags Docker image in GHCR with version tag
  6. Updates Helm chart version
  7. Notifies platform-bot to update release dashboard
```

**Tools:** `semantic-release` (free OSS) + GitHub Actions

**Repository:** `zs-agent-release-manager`

---

### Agent 8: `zs-agent-smoke-tester`

**Role:** Post-deployment automated health verification.

**Tests run after every deployment:**

```go
// FHIR Engine Health
POST /fhir/R5/Patient   → 201 Created  (create patient)
GET  /fhir/R5/Patient/{id} → 200 OK   (read back)
GET  /fhir/R5/metadata  → 200 OK       (capability statement)
GET  /fhir/R5/Patient?name=Test → 200 OK (search works)

// Auth
POST /auth/token → 200 OK (Keycloak token endpoint)

// Services
GET /services/patient/health → 200 OK
GET /services/encounter/health → 200 OK
GET /services/observation/health → 200 OK
GET /services/terminology/health → 200 OK

// Terminology
GET /fhir/R5/CodeSystem/$lookup?system=http://hl7.org/fhir/sid/icd-11&code=... → 200 OK
```

**On failure:**
1. Argo CD rollback triggered automatically
2. GitHub Issue opened with failure details
3. Slack/email notification to DevOps lead

**Repository:** `zs-agent-smoke-tester`

---

## GitHub Actions — Reusable Workflows

All reusable CI/CD workflows live in `zs-ops-github-actions/.github/workflows/`:

### Workflow: `go-test.yml`
```yaml
# Called by every Go service repository
name: Go Test
on:
  workflow_call:
    inputs:
      go-version: { required: true, type: string, default: '1.26.1' }
jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres: { image: postgres:18, env: { POSTGRES_PASSWORD: test } }
      nats: { image: nats:2.10.24 }
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-go@v5
        with: { go-version: ${{ inputs.go-version }} }
      - run: go test ./... -race -coverprofile=coverage.out
      - run: golangci-lint run
      - uses: codecov/codecov-action@v4   # free for OSS
```

### Workflow: `fhir-validate.yml`
```yaml
# Called by content and data repositories
name: FHIR Validation
on: [workflow_call]
jobs:
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Validate FHIR resources
        run: |
          # Use HAPI FHIR validator CLI (free, Apache 2.0)
          java -jar validator_cli.jar \
            -version 5.0.0 \
            -ig zs-data-fhir-profiles/ \
            resources/**/*.json
```

### Workflow: `helm-release.yml`
```yaml
# Packages and pushes Helm charts to GHCR
name: Helm Release
on: [workflow_call]
jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: helm lint .
      - run: helm package .
      - run: helm push *.tgz oci://ghcr.io/zarishsphere/charts
```

---

## No-Code Operation Guide

As a platform owner, here is what you do — all in GitHub browser:

| Task | How | Repository |
|------|-----|-----------|
| Propose a new feature | Open Discussion → `[RFC] Feature Name` | `zs-docs-rfc` |
| Approve a PR | Click "Approve" on the PR | Any repository |
| Merge a PR | Click "Merge" (automation does the rest) | Any repository |
| Add a new clinical form | Use Form Builder UI → PR auto-created | `zs-content-forms-*` |
| Onboard a new country | Fork `zs-iac-template-country` → Fill checklist | New `zs-infra-{code}` |
| View system health | Visit health.zarishsphere.com | — |
| View API docs | Visit docs.zarishsphere.com/api | — |
| Check security issues | Review Security tab in any repo | GitHub Security Advisories |
| Update dependencies | Approve Renovate PR (auto-opened weekly) | Any repository |
| Deploy to production | Merge PR to main → Argo CD does the rest | Any service repo |

---

## Automation Event Map

```
Events that trigger automation in ZarishSphere:

PR Opened
  → Auto-labeler runs (platform-bot)
  → Code review bot queued (agent-code-review)
  → Security scan starts (agent-security-scanner)
  → Content validator runs if content repo (agent-content-validator)

PR Merged to main
  → Docker build + GHCR push (go-build workflow)
  → Helm chart updated (helm-release workflow)
  → Argo CD detects new image → syncs cluster
  → Smoke tests run (agent-smoke-tester)
  → If smoke tests pass → Release notes generated (agent-release-manager)
  → If smoke tests fail → Argo CD rollback + GitHub Issue opened

RFC Discussion Opened
  → Platform bot labels: "rfc:draft"
  → Bot comments with RFC template checklist
  → After 14 days → Bot pings owners for vote

New Repository Created
  → Platform bot adds: branch protection, required reviewers
  → Renovate config applied
  → Security scanner enabled
  → README template applied

Weekly (every Monday):
  → Dependency update PRs opened (Renovate)
  → Security scan report (agent-security-scanner)
  → Stale PR report (platform-bot)
  → Docs regenerated (agent-docs-generator)
```

---

> This document governs all automation decisions. Changes require RFC-0003 update.
