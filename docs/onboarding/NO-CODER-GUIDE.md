# ZarishSphere No-Coder Guide

> **For:** Platform owners, health program managers, country coordinators
> **What you need:** A web browser and a GitHub account. That's it.
> **What you don't need:** Terminal, code editor, programming knowledge

---

## Welcome

This guide is written specifically for you — someone who manages a health program, not someone who writes software. You own this platform. You make the decisions. You don't need to write a single line of code.

Everything in ZarishSphere is designed to be governed through a web browser at github.com.

---

## Your Daily Responsibilities (All in GitHub Browser)

### ✅ Review and Approve Pull Requests

When developers make changes, they open a "Pull Request" (PR). Your job is to:

1. Go to https://github.com/orgs/zarishsphere (your organization)
2. Click on any repository name
3. Click the "Pull Requests" tab
4. Open a PR and read the description
5. Scroll through the "Files changed" tab to see what changed
6. If you're happy: click **"Review changes"** → **"Approve"** → **"Submit review"**
7. Then click **"Merge pull request"** (green button)

The automation (bots) will then deploy the change automatically.

---

### ✅ Propose a New Feature (RFC)

When you want something new built:

1. Go to https://github.com/zarishsphere/zs-docs-rfc/issues/new/choose
2. Click **"RFC / Feature Request"**
3. Fill in the form in plain language:
   - What problem are you trying to solve?
   - What do you want the platform to do?
   - Who benefits from this?
4. Click **"Submit new issue"**
5. Owners and community will discuss it for 14 days
6. If accepted, developers will build it

---

### ✅ Write or Update Documentation

1. Navigate to the file you want to edit (e.g., this one)
2. Click the **pencil icon** ✏️ at the top right of the file
3. Make your edits (the editor is like a simple word processor)
4. Scroll to the bottom — write a short description of what you changed
5. Click **"Propose changes"** (green button)
6. On the next screen click **"Create pull request"**

A reviewer will check your edit and merge it within a few days.

---

### ✅ Approve a New Country's Onboarding

1. Go to https://github.com/zarishsphere/zs-docs-camm
2. Open the file for the country (e.g., `countries/BGD-STATUS.md`)
3. Read the current CAMM level and checklist
4. To advance a country to the next level, click ✏️ and check off completed items
5. Open a Pull Request for review

---

### ✅ File a Bug Report

If something is broken:

1. Go to the repository where the problem is
2. Click **"Issues"** tab → **"New issue"**
3. Choose **"Bug Report"** template
4. Describe:
   - What you were trying to do
   - What you expected to happen
   - What actually happened
   - Screenshots if possible
5. Submit — the technical team will investigate

---

### ✅ Check System Health

Bookmark these URLs to check health any time:

| What | URL | What You See |
|------|-----|-------------|
| All systems | https://health.zarishsphere.com | Green = good, Red = problem |
| GitHub Actions (CI) | https://github.com/orgs/zarishsphere/actions | Build status |
| Grafana dashboards | https://grafana.zarishsphere.com | Usage metrics |

---

### ✅ Add a New Clinical Form

1. Go to https://github.com/zarishsphere/zs-ui-form-builder (the no-code form builder)
2. Log in with your ZarishSphere account
3. Click **"New Form"**
4. Drag and drop fields from the left panel
5. Fill in the FHIR mapping for each field (the system guides you)
6. Click **"Save as Draft"**
7. Click **"Submit for Review"** — this opens a PR automatically

---

## Understanding the GitHub Interface

### What is a Repository?

A repository ("repo") is like a folder that contains a project. ZarishSphere has 211 repositories, each for a specific part of the platform. Think of them like departments in an organization.

### What is a Pull Request?

A Pull Request (PR) is a proposed change. Before any change goes into the platform, it must be reviewed and approved. This is your quality control gate.

### What is an Issue?

An Issue is a task, bug report, or proposal. It's how the team tracks work. When you file a bug or propose a feature, you create an Issue.

### What is a Branch?

When someone makes changes, they work on a "branch" — a separate copy of the code. When ready, they merge it into "main" (the live version). You don't need to manage branches.

---

## Your Owner Permissions

As a platform owner, you can:
- ✅ Approve and merge any Pull Request
- ✅ Create, edit, and delete any file
- ✅ Add or remove contributors
- ✅ Configure repository settings
- ✅ Vote on RFCs
- ✅ Create GitHub Issues and Milestones

---

## Getting Help

| Issue | Contact |
|-------|---------|
| GitHub question | Open issue in zs-docs-platform |
| Clinical content question | Email healthbgd@cpintl.org |
| Technical problem | Email code.and.brain@gmail.com |
| Platform governance | Email zarishsphere@gmail.com |

---

## Quick Reference: Key URLs

| Purpose | URL |
|---------|-----|
| Your organization | https://github.com/orgs/zarishsphere |
| All repositories | https://github.com/orgs/zarishsphere/repositories |
| Open Pull Requests | https://github.com/pulls?q=org%3Azarishsphere |
| Open Issues | https://github.com/issues?q=org%3Azarishsphere |
| RFC proposals | https://github.com/zarishsphere/zs-docs-rfc/issues |
| ADR decisions | https://github.com/zarishsphere/zs-docs-adr |
| CAMM tracking | https://github.com/zarishsphere/zs-docs-camm |
| Platform docs | https://docs.zarishsphere.com |

---

*You are the platform owner. The technology works for you, not the other way around.*
