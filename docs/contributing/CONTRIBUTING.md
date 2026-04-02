# Contributing to ZarishSphere

> **Version:** 2.0.0 | **Updated:** 2026-03-24
> Welcome! ZarishSphere is built in public. Every contribution matters.

---

## Table of Contents

1. [Who Can Contribute](#who-can-contribute)
2. [Before You Start](#before-you-start)
3. [Types of Contributions](#types-of-contributions)
4. [Contribution Process](#contribution-process)
5. [RFC Process](#rfc-process)
6. [Code Standards](#code-standards)
7. [Commit Convention](#commit-convention)
8. [Pull Request Rules](#pull-request-rules)
9. [Review Process](#review-process)
10. [No-Code Contributions](#no-code-contributions)

---

## 1. Who Can Contribute

**Everyone.** ZarishSphere is an open-source platform under the Apache 2.0 license.

We especially welcome contributions from:
- Health program managers and clinicians (clinical content, forms, protocols)
- Country program staff (translations, country-specific configs)
- Software developers (Go, TypeScript, Flutter)
- DevOps engineers (Kubernetes, OpenTofu, Argo CD)
- Technical writers (documentation, runbooks)
- Community members (bug reports, testing, feedback)

You do **not** need to be a coder to contribute. See [No-Code Contributions](#no-code-contributions).

---

## 2. Before You Start

### Read these documents first:
1. [PHILOSOPHY.md](../platform/PHILOSOPHY.md) — the 8 platform commitments you must respect
2. [CODE-STANDARDS.md](./CODE-STANDARDS.md) — coding rules for all languages
3. [COMMIT-CONVENTION.md](./COMMIT-CONVENTION.md) — how to write commit messages

### Set up your environment:
- GitHub account (free)
- Git installed locally, or use github.com browser editor
- For code contributions: follow [LOCAL-SETUP.md](../onboarding/LOCAL-SETUP.md)

---

## 3. Types of Contributions

| Type | Description | Process |
|------|-------------|---------|
| **Bug report** | Something is broken | Open Issue → Bug Report template |
| **Feature proposal** | New capability | Open Issue → RFC template → RFC process |
| **Clinical form** | New JSON Schema form | PR to `zs-content-forms-*` |
| **Translation** | New language support | PR to `zs-data-translations` |
| **Documentation** | Fix or improve docs | PR directly (no RFC needed for minor fixes) |
| **Code** | Go, TypeScript, Flutter | RFC → PR with tests |
| **Infrastructure** | OpenTofu, Helm, K8s | RFC → PR |
| **Security issue** | Vulnerability | See [SECURITY.md](../../SECURITY.md) — do not open a public issue |

---

## 4. Contribution Process

### For small changes (typos, minor doc fixes, translation additions):
1. Fork the repository on GitHub
2. Make your change
3. Open a Pull Request
4. One owner reviews and merges

### For significant changes (new features, new clinical forms, architecture):
1. Open an RFC Issue in `zs-docs-rfc` (see [RFC Process](#rfc-process))
2. Wait for community comment period (14 days)
3. RFC accepted by owners vote
4. Open a Pull Request implementing the RFC
5. Two owners review and merge

---

## 5. RFC Process

RFC = Request for Comments. All significant changes to ZarishSphere must begin as an RFC.

**When is an RFC required?**
- Adding a new service or repository
- Changing a FHIR resource structure
- Changing an API interface
- Adding a new technology dependency
- Changing authentication or security policy
- Onboarding a new country

**RFC lifecycle:**
```
Draft → Open for Comment (14 days) → Owner Vote (2 of 4 required) → Accepted or Rejected
```

**How to write an RFC:**
1. Go to https://github.com/zarishsphere/zs-docs-rfc/issues/new
2. Use the RFC template
3. Title format: `[RFC] Your Proposal Title`
4. Fill in all sections completely

---

## 6. Code Standards

### Go (1.26.1)
- Use `chi v5` for HTTP routing — NOT gin, echo, or fiber
- Use `pgx v5` for PostgreSQL — all queries must be parameterized
- Use `zerolog` for logging — NEVER `fmt.Println` or `log.Print`
- Use `viper` for configuration — no hardcoded values
- Every function that can fail must return an error
- All errors must be wrapped: `fmt.Errorf("context: %w", err)`
- Use `errors.Is` and `errors.As` for error checking
- NEVER use `panic()` in production code
- Context (`ctx`) must be the first parameter of every function
- Every service must have OpenTelemetry spans on external calls
- Every FHIR operation must generate an AuditEvent

### TypeScript (6.0) / React (19.x)
- Strict mode enabled — zero `any` types
- All components must be functional (no class components)
- Use `React Hook Form` + `Zod` for forms
- Use `TanStack Query v5` for server state
- Use `Zustand 5` for global state — no Redux
- WCAG 2.2 AA accessibility required on all UI components
- Carbon Design System components preferred over custom UI

### Clinical Forms (JSON Schema)
- Every field MUST have a `fhirPath` mapping
- Coded fields MUST have a LOINC, SNOMED, or ICD-11 code
- Every label MUST be an i18n key (not inline text)
- Every key MUST exist in `zs-data-translations` (EN + BN minimum)
- Forms MUST validate against ZS Form Schema v1

---

## 7. Commit Convention

ZarishSphere uses [Conventional Commits](https://www.conventionalcommits.org/).

**Format:**
```
<type>(<scope>): <short description>

[optional body]

[optional footer]
Closes #<issue-number>
```

**Types:**
| Type | When to use |
|------|------------|
| `feat` | New feature or capability |
| `fix` | Bug fix |
| `docs` | Documentation changes |
| `refactor` | Code restructuring (no behaviour change) |
| `test` | Adding or updating tests |
| `chore` | Maintenance, dependency updates |
| `ci` | CI/CD pipeline changes |
| `content` | Clinical form, protocol, or terminology changes |
| `perf` | Performance improvement |
| `style` | Formatting (no logic change) |

**Examples:**
```
feat(patient): add Jaro-Winkler duplicate detection
fix(fhir): correct AuditEvent resource type for Observation read
docs(camm): add BGD CAMM L1 completion checklist
content(forms): add PHQ-9 mental health screening form
chore(deps): update pgx to v5.7.2
```

**Breaking changes** — add `!` after type:
```
feat!(fhir)!: change Patient resource schema to include tenant_id
```

---

## 8. Pull Request Rules

Every PR must:
- [ ] Have a descriptive title following the commit convention
- [ ] Be linked to a GitHub Issue or RFC
- [ ] Have a completed PR description (what changed and why)
- [ ] Pass all CI checks (tests, lint, security scan)
- [ ] Include tests for any new functionality
- [ ] Have no secrets, credentials, or PHI in any file
- [ ] Have an updated CHANGELOG.md entry
- [ ] Be reviewed by at least 1 owner before merge

PRs touching security, FHIR engine, or clinical data require 2 owner reviews.

---

## 9. Review Process

**Review turnaround:**
- P0 (critical bug fix): Within 24 hours
- P1 (important feature): Within 3 business days
- P2 (standard PR): Within 5 business days
- P3 (documentation/chore): Within 7 business days

**Reviewers check:**
- Does this respect all 8 platform commitments?
- Does this follow the coding standards for its language?
- Is there adequate test coverage?
- Are there any security concerns?
- Is the documentation updated?

---

## 10. No-Code Contributions

You do not need to write any code to contribute meaningfully.

**GitHub browser is all you need for:**
- Writing or improving documentation
- Reviewing and commenting on PRs
- Filing bug reports with clear reproduction steps
- Writing RFC proposals in plain language
- Translating UI strings in `zs-data-translations`
- Writing clinical protocols in `zs-content-protocols`
- Updating country status in `zs-docs-camm`
- Creating donor reports in `zs-docs-donor`

**How to edit a file in GitHub browser:**
1. Navigate to the file on github.com
2. Click the ✏️ pencil icon (top right of file view)
3. Make your changes
4. Scroll down to "Commit changes"
5. Write a commit message following the convention
6. Select "Create a new branch for this commit and start a pull request"
7. Click "Propose changes"

---

*Thank you for contributing to a healthier world.*
*Questions? Open an issue or email platform@zarishsphere.com*
