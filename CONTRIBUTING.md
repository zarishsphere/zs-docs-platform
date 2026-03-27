# Contributing to ZarishSphere

> **Version:** 1.0.0 | **Last Updated:** 2026-03-27

Thank you for your interest in contributing to ZarishSphere! This guide tells you everything needed to contribute code, documentation, or clinical content.

---

## Getting Started (5 Minutes)

### 1. Fork and Clone

```bash
git clone https://github.com/zarishsphere/<repo-you-want>.git
cd <repo-you-want>
```

### 2. Set Up Your Environment

Each repository has its own setup guide. Start with:

```bash
# For Go services:
make dev          # Starts local PostgreSQL 18.3, NATS 2.12.5, Valkey 9.0.3, etc.
make test         # Runs tests

# For frontend:
npm install
npm run dev

# For mobile (Flutter):
flutter pub get
flutter run
```

### 3. Make a Branch

```bash
git checkout -b feat/my-feature
# or
git checkout -b fix/my-bugfix
```

Follow [Naming Convention](./docs/governance/NAMING-STANDARDS.md) for branches.

---

## Before You Code

### Check the RFC Log

Before proposing a major feature:

1. **Search RFCs** → Does this idea already have an RFC? (See [zs-docs-rfc](https://github.com/zarishsphere/zs-docs-rfc/tree/main/drafts))
2. **If no RFC exists** → Open an RFC discussion (see RFC-PROCESS.md)
3. **Wait for acceptance** → Your PR will be blocked if no RFC exists for major changes

**Rule:** Major architectural changes (new services, schema changes, new repos) require an RFC first.

### Check the Architecture

Is your change aligned with:

- ✅ [ARCHITECTURE.md](./docs/platform/ARCHITECTURE.md) — system design
- ✅ [ADRs](https://github.com/zarishsphere/zs-docs-adr) — architectural decisions
- ✅ [TECHNOLOGY-STACK.md](./docs/platform/TECHNOLOGY-STACK.md) — approved versions

---

## Code Standards

### Go (Backend)

All Go services use **Go 1.26.1**. Standard enforced by CI:

```bash
# Before pushing:
go fmt ./...
go vet ./...
golangci-lint run

# Tests MUST pass:
go test ./... -race -coverprofile=coverage.out
# Coverage MUST be ≥ 80%
```

**Key rules:**
- No `fmt.Println` — use `zerolog` only
- No bare `panic()` — use `errors.Is()` / `errors.As()`
- All external calls get OpenTelemetry spans
- All HTTP handlers need context propagation
- Database queries use pgx v5 with parameterized queries (NO string concat)
- Tests use testcontainers-go for integration tests

**Example:**

```go
package main

import (
	"context"
	"github.com/go-chi/chi/v5"
	"github.com/jackc/pgx/v5/pgxpool"
	"github.com/rs/zerolog"
)

func CreatePatient(w http.ResponseWriter, r *http.Request) {
	ctx := r.Context()
	log := zerolog.Ctx(ctx)

	// Use pgx for safe queries
	var patientID string
	err := pool.QueryRow(ctx,
		"INSERT INTO patient (name, tenant_id) VALUES ($1, $2) RETURNING id",
		name, tenantID,
	).Scan(&patientID)
	if err != nil {
		log.Error().Err(err).Msg("failed to create patient")
		http.Error(w, "Internal error", http.StatusInternalServerError)
		return
	}

	w.Header().Set("Content-Type", "application/fhir+json")
	json.NewEncoder(w).Encode(patient)
}
```

### TypeScript/React (Frontend)

All frontend code uses **TypeScript 6.0** + **React 19.x** + **Next.js 16.2**.

```bash
# Before pushing:
npx prettier --write .
npx eslint . --fix
npm run type-check
npm run test -- --coverage
```

**Key rules:**
- Strict TypeScript (no `any`)
- All components are functional + hooks
- Use Zustand for global state
- Use React Query for server state
- All forms use React Hook Form + Zod validation
- Tests use Vitest + React Testing Library

### Flutter/Dart (Mobile)

All mobile code uses **Flutter 3.41.2** + **Dart 3.11**.

```bash
# Before pushing:
dart format .
dart analyze
flutter test --coverage
```

**Key rules:**
- Use Riverpod for state management
- All async work in providers
- Use null-safety
- Tests cover core business logic
- Build scripts for Android + iOS

---

## Commit Convention

Follow [Conventional Commits](./COMMIT-CONVENTION.md):

```
feat: add patient search by medical record number
fix: correct LOINC code for blood glucose
docs: update installation guide
chore: upgrade Go to 1.26.1
ci: add Trivy security scan to CI
```

**Format:**
```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat` — New feature
- `fix` — Bug fix
- `docs` — Documentation
- `style` — Formatting (no logic change)
- `refactor` — Logic change (no behavior change)
- `perf` — Performance improvement
- `test` — Tests only
- `chore` — Dependencies, tooling
- `ci` — CI/CD configuration

**Example:**

```
feat(svc-patient): add bulk patient import via CSV

- Parse CSV with patient demographics
- Create FHIR Patient resources
- Generate AuditEvent for each import
- Add integration tests with testcontainers-go

Fixes #42
```

---

## Pull Request Process

### 1. Push Your Branch

```bash
git push origin feat/my-feature
```

### 2. Create Pull Request

- **Title:** Conventional commit style (e.g., `feat: add patient search`)
- **Description:** Link issue, explain what changed
- **Link RFC:** If applicable (e.g., "Implements RFC-0005")

### 3. Check Status

Your PR automatically triggers:

✅ **Tests** — `go test`, `npm test`
✅ **Linting** — `golangci-lint`, `eslint`
✅ **Security** — Trivy, CodeQL, GitGuardian
✅ **Coverage** — Must be ≥ 80% on new code
✅ **Code Review** — AI bot + human review

### 4. Address Feedback

```bash
# Make requested changes
git add .
git commit -m "fix: address PR review feedback"
git push
# PR auto-updates
```

### 5. Merge

Maintainers will merge if all checks pass. For patch/minor bumps, we auto-merge.

---

## FHIR Clinical Content

### Adding a Clinical Form

1. Create JSON Schema profile in [zs-content-forms-*](https://github.com/zarishsphere/zs-content-forms-core)
2. **Every field MUST have:**
   - FHIR mapping (fhirPath)
   - LOINC or SNOMED code (if appropriate)
   - Multilingual labels (EN, BN, MY, UR, HI, TH keys in zs-data-translations)

Example:

```json
{
  "type": "object",
  "properties": {
    "weight": {
      "type": "number",
      "description": "Patient weight in kg",
      "fhirPath": "Observation.value.ofType(Quantity).value",
      "fhirMapping": {
        "resourceType": "Observation",
        "code": { "coding": [{ "system": "http://loinc.org", "code": "29463-7" }] }
      },
      "i18n": { "en": "Weight", "bn": "ওজন" }
    }
  }
}
```

### Adding Terminology

1. Add codes to appropriate data repo:
   - ICD-11 → [zs-data-icd11](https://github.com/zarishsphere/zs-data-icd11)
   - SNOMED → [zs-data-snomed](https://github.com/zarishsphere/zs-data-snomed)
   - LOINC → [zs-data-loinc](https://github.com/zarishsphere/zs-data-loinc)
2. Add concept map (if translating between terminologies)
3. Add ValueSet definition (if creating a value set)

---

## Documentation

Every repository needs:

✅ `README.md` — What does this repo do?
✅ `CONTRIBUTING.md` — How to contribute to THIS repo
✅ `.github/CODEOWNERS` — Who reviews PRs?
✅ `Makefile` — How to build/test locally
✅ OpenAPI spec (for services) — API documentation
✅ Helm chart (for services) — How to deploy

For documentation repos, add:

✅ Well-structured folder `/docs` with `.md` files
✅ Internal links between docs
✅ Examples for every feature

---

## Security & Secrets

### ❌ NEVER commit:

- API keys, tokens, passwords
- Database credentials
- Private keys
- Secrets in code or config

### ✅ DO use Vault:

All secrets go in HashiCorp Vault (free OSS). Environmental references only:

```bash
# ✅ Good
DATABASE_URL=postgres://user:${DB_PASSWORD}@localhost/db

# ❌ Bad
DATABASE_URL=postgres://user:super_secret_password@localhost/db
```

See [Security Policy](https://github.com/zarishsphere/zs-docs-security) for details.

---

## Testing

### Go Services

```bash
# Unit + integration tests with testcontainers
go test ./... -race -coverprofile=coverage.out

# Minimum 80% coverage
go tool cover -html=coverage.out
```

### Frontend

```bash
# Unit tests with Vitest
npm run test

# E2E tests with Playwright
npm run test:e2e
```

### Mobile (Flutter)

```bash
# Run tests with coverage
flutter test --coverage
```

---

## Signing Your Work

All commits MUST be signed:

```bash
git config user.signingkey <your PGP key ID>
git commit -S -m "feat: ..."
```

Or configure auto-signing:

```bash
# ~/.gitconfig
[commit]
  gpgSign = true
```

---

## Questions?

- **Documentation Question?** Open issue in [zs-docs-platform](https://github.com/zarishsphere/zs-docs-platform/issues)
- **Architecture Question?** Open issue in [zs-docs-adr](https://github.com/zarishsphere/zs-docs-adr/issues)
- **RFC Question?** Open issue in [zs-docs-rfc](https://github.com/zarishsphere/zs-docs-rfc/issues)
- **Code Question?** Ask in repo's discussions tab

---

## Code of Conduct

All contributors agree to follow the [Code of Conduct](../.github/CODE_OF_CONDUCT.md).

We value:
✅ Respectful communication
✅ Inclusive collaboration
✅ Focus on ideas, not people
✅ Everyone belongs here

---

> **If it is not documented, it does not exist.**

See also:
- [Architecture](./docs/platform/ARCHITECTURE.md)
- [RFC Process](./docs/governance/RFC-PROCESS.md)
- [ADR Process](./docs/governance/ADR-PROCESS.md)
- [Naming Standards](./docs/governance/NAMING-STANDARDS.md)
