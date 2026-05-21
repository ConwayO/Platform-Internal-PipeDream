# Platform-Internal-PipeDream

> **Created by** Magen Naidoo (ZA-DUR) · **Last updated** May 17, 2026 · 4 minute read  
> **Format:** One-day hackathon — deliver a 15-minute recording by end of day

---

## Context

We run two CI ecosystems side-by-side — **Azure DevOps Pipelines** and **GitHub Actions** — and neither currently has a review experience as capable as GitHub Copilot's native PR integration.

We want our own: an **agentic review system** that sits inside the pipelines, understands the change, and posts genuinely useful feedback — not just lint noise — to the developer who raised the PR.

### The differentiator: sophistication via specialisation

Rather than one big "review the diff" prompt, contestants should architect a **multi-agent system** where each agent has a focused remit, and an orchestrator fans out, gathers, deduplicates, and posts back a coherent review.

---

## Goal

Build a functioning multi-agent PR review system that:

| | Requirement | Priority |
|---|---|---|
| 🔁 | **Auto-trigger** — fires automatically when a PR is opened or updated, no manual kickoff | Must-have |
| 🔧 | **Pipeline-native** — runs inside AzDo Pipelines and GitHub Actions | Must-have |
| 🤖 | **Multi-agent** — multiple specialised agents, not a single monolithic prompt | Must-have |
| 💬 | **PR comments** — inline comments on relevant lines + summary at top of PR | Must-have |
| ⚙️ | **Config gating** — advisory by default; can fail the build on configurable severity thresholds | Must-have |

---

## Core Requirements

### 1. PR Trigger
Agent runs automatically on **PR open** and **PR update** events on both platforms. Zero manual intervention.

### 2. Both Pipelines
Working integration on **Azure DevOps Pipelines** and **GitHub Actions**. Same agent core — two thin pipeline wrappers.

### 3. Multi-Agent Architecture

At minimum, four specialised agents plus one orchestrator:

| Agent | Remit |
|---|---|
| 🔍 **Code Quality / Maintainability** | SOLID principles, readability, naming conventions, cyclomatic complexity |
| 🔐 **Security** | OWASP, secrets in code, injection vectors, auth/authz gaps |
| 🧪 **Test Coverage & Quality** | Does new code have tests? Are they meaningful? Coverage delta analysis |
| 🏗️ **Architecture / Design Fit** | Does this change cohere with surrounding patterns? Structural drift detection |
| 🎼 **Orchestrator** | Fans out in parallel, deduplicates overlapping findings, resolves conflicts, produces final review |

### 4. Multi-Model Strategy

Multi-model is **explicitly called out in judging**. Pick the right model for each agent — fast/cheap for lint-style work, stronger for architectural reasoning. Justify your choices in the recording.

### 5. Comments Back to PR
- Inline comments on the relevant diff lines where possible
- A summary comment pinned at the top of the PR
- Must render cleanly in both AzDo and GitHub

### 6. Configurable Gating

A `.pr-review.yaml` config file controlling:

```yaml
severity_thresholds:
  block_on: [CRITICAL, HIGH]   # which severities gate the build

agents:
  code_quality: true
  security: true
  test_coverage: true
  architecture: true

block-on-critical: true
```

Severity levels: `CRITICAL` · `HIGH` · `MEDIUM` · `LOW`

### 7. Valuable Output

Comments must be **specific, actionable, and tied to the diff** — not generic advice.  
Judged on quality, not volume. The test: *would a developer thank the agent, or mute it?*

---

## Available Capability — DomainExpert

**DomainExpert** is an internal capability: any agent can query it to retrieve semantically relevant context from across the codebase.

> Treat DomainExpert as a first-class tool, not a bolt-on.

A diff in isolation is thin context. Grounding the review against the rest of the codebase fundamentally changes the quality bar. Entries that use it well will stand out — and it carries **bonus judging weight**.

---

## Bonus Tiers

Think of the pipeline as a platform for review-time intelligence. Execute any of these well and you score notably higher. Pick **one or two and do them properly** rather than four superficially.

| | Bonus |
|---|---|
| 🌐 | Org-Scale Rollout |
| 🔬 | SonarQube Integration |
| 🛡️ | Dependency & Vulnerability Scanning |
| ⚡ | Performance Regression Hints |
| 📐 | Architecture Decision Drift |

---

## Out of Scope

> These will **disqualify or void** your entry.

- ❌ **No SaaS wrappers** — wrapping CodeRabbit, Greptile, or similar is an instant disqualification. Build the agent system yourselves.
- ❌ **No auto-fix / auto-commit** — agents may suggest changes in comments but must not push code, amend the PR, or auto-merge.
- ❌ **No proprietary code exfil** — use the internal LiteLLM proxy when reviewing company code.

---

## Deliverables

| | Deliverable | Detail |
|---|---|---|
| 🎥 | **15-minute recording** | Architecture overview (1–2 min) · Live trigger demo on both AzDo and GitHub (5–6 min) · Walkthrough of actual review comments on a real PR (4–5 min) · Bonus integrations and org-scale rollout thinking (2–3 min) |
| 📁 | **Source code (repo link)** | Agent code · AzDo pipeline YAML · GitHub Actions workflow YAML · config schema for gating |
| 📄 | **One-page README** | How to install and wire it into a new repo from scratch |

---

## Installation

> *How to wire PipeDream into a new repo from scratch.*

### GitHub Actions

1. Copy `.github/workflows/pr-review.yml` into your target repo.
2. Add a `.pr-review.yaml` at the repo root and configure severity thresholds.
3. Set the required secrets (`LITELLM_API_KEY`, `GITHUB_TOKEN` is automatic).
4. Open a PR — the review fires automatically.

### Azure DevOps

1. Copy `azure-pipelines/pr-review.yml` into your target repo.
2. Add a `.pr-review.yaml` at the repo root.
3. Configure the pipeline trigger on PR events in your AzDo project settings.
4. Set pipeline variables: `LITELLM_API_KEY`, `AZURE_DEVOPS_PAT`.
5. Open a PR — done.

---

## Stretch Questions

Strong entries will have a clear opinion on all of these. Address them in your recording:

1. **How do your agents avoid duplicating each other's findings?**
2. **How do you keep comment volume low and signal high?**
3. **What does the on-ramp look like for a new team?** One line in their pipeline? An installable skill?
4. **Where does this break?** What's the failure mode when an agent hallucinates an API or misreads intent?
5. **How are you using DomainExpert (or equivalent)** to make the review richer than the diff alone?
6. **How would you measure whether this system is actually helping engineers over time?**

---

## Repository Structure

```
Platform-Internal-PipeDream/
├── .pr-review.yaml                  # Default config — severity thresholds & agent toggles
├── .github/
│   └── workflows/
│       └── pr-review.yml            # GitHub Actions trigger & wrapper
├── azure-pipelines/
│   └── pr-review.yml                # Azure DevOps trigger & wrapper
├── agents/
│   ├── orchestrator/                # Fan-out, dedup, conflict resolution, final post
│   ├── code-quality/                # SOLID, readability, complexity
│   ├── security/                    # OWASP, secrets, injection vectors
│   ├── test-coverage/               # Test presence, quality, coverage delta
│   └── architecture/                # Pattern coherence, structural drift
└── README.md
```

---

*PipeDream — because your pipeline deserves smarter reviews.*
