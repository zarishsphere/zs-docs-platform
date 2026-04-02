# RFC Process — Request for Comments

> **Document:** RFC-PROCESS.md | **Version:** 2.0.0
> **Maintained in:** zs-docs-platform (authoritative) and zs-docs-rfc (reference)

---

## What Is an RFC?

An RFC (Request for Comments) is a proposal document. Every significant change to ZarishSphere must begin as an RFC. This ensures:
- All stakeholders can provide input before work begins
- Decisions are transparent and documented
- No surprises after implementation starts

---

## When Is an RFC Required?

**Required (mandatory):**
- Adding a new repository
- Changing a FHIR resource structure
- Changing an API interface (breaking or non-breaking)
- Adding a new programming language or framework
- Changing authentication or authorization policy
- Onboarding a new country
- Changing the CAMM framework
- Modifying this RFC process

**Not required (PR directly):**
- Bug fixes
- Documentation improvements
- Translation additions
- Dependency version updates (handled by Renovate)
- Typo fixes
- Minor UI styling changes

---

## RFC Lifecycle

```
[GitHub Issue opened]
        ↓
    rfc:draft  ─────────── 14 days open for community comment
        ↓
    rfc:review ─────────── Owner vote (2 of 4 required to accept)
        ↓              ↓
  rfc:accepted     rfc:rejected
        ↓              ↓
  File moved to    File moved to
  accepted/        rejected/
```

---

## How to Write an RFC

### 1. Open an Issue in zs-docs-rfc

Go to: https://github.com/zarishsphere/zs-docs-rfc/issues/new/choose

Select **"RFC / Feature Request"** template.

### 2. Fill in All Sections

```markdown
## RFC Summary
One paragraph. What are you proposing?

## Problem / Motivation
What problem does this solve? Who is affected?
Include evidence: user quotes, metrics, field observations.

## Proposed Solution
Describe your solution in detail.
Include diagrams if helpful.

## Alternatives Considered
What else did you consider? Why did you reject those alternatives?
Minimum 2 alternatives required.

## FHIR Resources Involved
List any FHIR R5 resources this affects (Patient, Observation, etc.)

## Platform Commitments Impact
Does this affect any of the 8 non-negotiable commitments?
If yes, explain how compliance is maintained.

## Estimated Effort
Small (< 1 week) / Medium (1–4 weeks) / Large (> 4 weeks)

## Success Criteria
How will we know this RFC is successfully implemented?
List measurable outcomes.

## References
- Links to related issues, ADRs, or external documentation
```

### 3. Add Labels and Assign

The platform bot (`zs-agent-platform-bot`) will automatically:
- Add `rfc:draft` label
- Assign to milestone "RFC Review"
- Ping `@arwa-zarish` for awareness

### 4. Community Comment Period (14 days)

Leave your RFC open for 14 calendar days. Anyone can comment.
Respond to comments constructively. Update your RFC as needed.

### 5. Owner Vote

After 14 days, the platform bot pings owners to vote:
- 2 of 4 owners must vote `rfc:accept` for acceptance
- 1 vote of `rfc:reject` with explanation pauses for discussion
- Unanimous `rfc:reject` closes the RFC as rejected

### 6. After Acceptance

1. Create a Markdown file: `accepted/RFC-NNNN-short-title.md`
2. Copy your issue content into the file
3. Add: `Status: Accepted`, `Date: YYYY-MM-DD`
4. Open a PR to add this file to `zs-docs-rfc`
5. If the RFC requires an ADR, create it in `zs-docs-adr`
6. Begin implementation

---

## RFC Numbering

RFCs are numbered sequentially: RFC-0001, RFC-0002, RFC-0003...

The platform bot assigns the number when an RFC moves to `rfc:review`.

---

## Existing Accepted RFCs

| RFC | Title | Date |
|-----|-------|------|
| RFC-0001 | Repository Naming Standards | 2026-03-16 |
| RFC-0002 | Repository Structure Standards | 2026-03-16 |

---

*All questions about the RFC process → open an issue in zs-docs-platform.*
