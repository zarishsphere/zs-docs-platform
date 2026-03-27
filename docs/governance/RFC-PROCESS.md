# RFC Process & Guidelines

> **Version:** 1.0.0 | **Last Updated:** 2026-03-27 | **Governance:** RFC-0001

---

## What is an RFC?

An **RFC (Request for Comments)** is a proposal for a significant change to ZarishSphere. RFCs ensure that major decisions are:

✅ Documented and transparent
✅ Discussed openly by the community
✅ Reviewed by technical leaders  
✅ Recorded in our decision log

---

## When Do You Need an RFC?

**YES, write an RFC for:**
- New microservices or architectural layers
- Changes to FHIR model or clinical data structure
- Major API design changes
- Infrastructure or deployment changes
- New country deployment model
- Dependency upgrades (major versions)
- Governance or naming convention changes

**NO, RFC not needed for:**
- Bug fixes
- Feature additions (on existing endpoints)
- Internal refactoring
- Documentation updates
- New forms or clinical content (follows zs-content-forms-* structure)

---

## RFC Lifecycle

### 1. DRAFT (Your Branch)

Write your RFC in a private draft. Include:

```markdown
# RFC-XXXX: Short Title

> **RFC Number:** XXXX | **Status:** DRAFT | **Date:** 2026-03-27
> **Author:** Your Name | **Stakeholders:** @person1 @person2

## Problem Statement
What problem does this solve?

## Proposed Solution
What's the change?

## Alternatives Considered
What else did you consider?

## Benefits
Why is this good?

## Risks
What could go wrong?

## Implementation Plan
How will we do this?

## Success Criteria
How do we know it worked?
```

### 2. REVIEW (GitHub Discussion)

1. **Open PR** to [zs-docs-rfc](https://github.com/zarishsphere/zs-docs-rfc/tree/main/drafts)
2. **GitHub Discussion** opens automatically
3. **Team discusses** — at least 1 week open
4. **Stakeholders weigh in** — architects, ops, product owner

### 3. ACCEPTED or REJECTED

- **ACCEPTED** → Join `accepted/` folder, get assigned ADRs
- **REJECTED** → Move to `rejected/`, document why

---

## RFC Template

Create file: `drafts/RFC-XXXX-short-title.md`

```markdown
# RFC-XXXX: Short Title

> **RFC Number:** XXXX | **Status:** DRAFT | **Date:** YYYY-MM-DD
> **Author:** Your Name (@github-handle) | **Updated:** YYYY-MM-DD

---

## Problem Statement

Describe the problem in 2-3 sentences. What pain point does this address?

## Current State

How does the system work today? What is broken or suboptimal?

## Proposed Solution

Describe your solution in detail.

### Design Diagram

```
If complex, include ASCII art or Mermaid diagram
```

### Example

Show how thiswould work in practice.

## Alternatives Considered

What else did you think about?

### Option A: ...
Pros / Cons

### Option B: ...
Pros / Cons

## Benefits

Why is this proposal better than alternatives?

- ✅ Benefit 1
- ✅ Benefit 2
- ✅ Benefit 3

## Risks & Mitigations

What could go wrong?

| Risk | Likelihood | Mitigation |
|------|------------|-----------|
| Risk 1 | High | Do this |
| Risk 2 | Low | Do that |

## Implementation Plan

How will we build this?

1. Phase 1 — Do X (Week 1-2)
2. Phase 2 — Do Y (Week 3)
3. Phase 3 — Do Z (Week 4+)

## Success Criteria

How do we know this worked?

- [ ] Metric 1 improves by X%
- [ ] System does Y without Z
- [ ] Team can Z in < N minutes

## Stakeholder Sign-off

- [ ] @code-and-brain (Technical Lead)
- [ ] @arwa-zarish (Platform Owner)
- [ ] @DevOps-Ariful-Islam (DevOps)

## Notes

- Link to related RFCs, ADRs, or issues
- Reference documentation

---

**Next steps:** Once accepted, an ADR will be created to document this decision.
```

---

## Governance Rules

### Who can accept RFCs?

- ✅ `@code-and-brain` (Technical Lead)
- ✅ `@arwa-zarish` (Platform Owner)
- ✅ `@DevOps-Ariful-Islam` (DevOps Architect)

Consensus required for acceptance.

### Timeline

- **Minimum discussion:** 7 days (to allow global team input)
- **Decision deadline:** 14 days (architecture group decides)
- **Appeal period:** 3 days (can request reconsideration)

### Status Badges

- 🟦 **DRAFT** — Open for discussion
- 🟩 **ACCEPTED** — Approved, ready to implement
- 🟥 **REJECTED** — Not approved, documented why
- 🟨 **SUPERSEDED** — Replaced by RFC-XXXX

---

## Example RFCs

- **RFC-0001:** [Repository Naming Standards](../accepted/RFC-0001-naming-standards.md)
- **RFC-0002:** [Repository Structure](../accepted/RFC-0002-repo-structure.md)
- **RFC-0003:** [Agent & Automation Specification](https://github.com/zarishsphere/zs-docs-rfc/discussions)

---

## FAQ

**Q: Can I skip the RFC for a small feature?**
A: If it touches architecture, APIs, or data models → RFC required. If it's a small feature on existing endpoints → no RFC needed.

**Q: How long is the review period?**
A: Minimum 7 days for community input, then architects decide within 14 days.

**Q: What if I disagree with a rejection?**
A: You have 3 days to request reconsideration. Request must include new evidence or addresses previous concerns.

---

See also: [ADR Process](./ADR-PROCESS.md)
