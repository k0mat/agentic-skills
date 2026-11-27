---
name: task-spec
description: >
  Author concise, workspace-grounded Markdown task specifications that capture
  problem, scope boundaries, exact system anchors, constraints, invariants,
  rollout risks, and deterministic acceptance criteria—structured as ideal
  input for `implementation-guide` or engineering handoff. Use this skill
  whenever the user asks to write down, draft, formalize, or spec out a task,
  work item, or engineering brief in Markdown. Do NOT trigger when the user
  wants you to execute the task directly, write a step-by-step how-to guide
  (use `implementation-guide`), or only clean up a rough prompt inline (use
  `input-refiner`).
---

# Task Specification Authoring

Produce concise, grounded Markdown task specifications that define **what** must change, **why**, **exact system anchors**, **constraints & decisions**, **invariants/rollout risks**, and **verifiable acceptance criteria**—without drifting into step-by-step **how** instructions. A downstream author or `implementation-guide` run must be able to plan and write an executable guide from this document without rediscovering paths, guessing boundaries, or inventing requirements.

## Workflow

### 1. Ground Before Writing
- Inspect relevant workspace files, configs, schemas, routes, or existing docs in **one bounded read-only pass**.
- Anchor every reference to real file paths, symbol names, API endpoints, database tables, CLI flags, or UI locations.
- Mark unverifiable external premises explicitly as `[Assumption: <concise premise>]`—never invent paths, flags, schemas, or defaults.
- **Output path**: Honor any user-specified path; otherwise follow existing repo task/spec conventions (e.g., `docs/tasks/<slug>.md`, `tasks/<slug>.md`) or default to `./<slug>-task.md`.

### 2. Separate Contract From Implementation
- Capture **requirements, contracts, boundaries, and invariants**—not numbered implementation steps, diffs, or full code blocks.
- Short schema, payload, or interface snippets are allowed **only** when specifying a required external contract.
- If a high-impact, hard-to-reverse architectural or product fork remains unresolved, surface it explicitly as `[Open Decision]` under `## Constraints & Decisions` (or resolve it with `decision-gate` before finalizing) instead of silently picking a side.

### 3. Draft With the Task Spec Structure

Adapt this template to the task:

````markdown
# Task: <Action Verb + Specific Outcome>

## Problem & Goal
<2-4 sentences: current state and concrete problem, desired end state, and why this change is needed now.>

## Scope
### In Scope
- <Concrete deliverable, behavior change, or contract update>

### Out of Scope
- <Explicit exclusion to prevent scope creep or ambiguity>

## Grounded Context
<!-- Real files, symbols, schemas, routes, configs, or external systems touched or depended on. -->
| Target | Current Role / Behavior | Relevance |
| :--- | :--- | :--- |
| `<path/to/file.ts#symbol>` | <What it currently does> | <Must change / Must preserve / Caller> |

## Constraints & Decisions
- **Constraint:** <Hard technical, security, compatibility, or business requirement>
- **Settled:** <Chosen approach and concise reason>
- **Rejected:** <Considered option> — <Concrete tradeoff or constraint violation>
<!-- Include only real constraints, settled choices, genuinely rejected options, or [Open Decision] items. -->

## Prerequisites & Environment
<!-- Omit section if none apply. -->
- <Required access, runtime versions, secrets/env vars, prior artifacts, or target environments>
- `[Assumption: <Unverifiable external premise, if any>]`

## Risks, Invariants & Rollout
- **Invariants:** <State, ordering, idempotency, or compatibility guarantees that must not break>
- **Failure modes:** <Concrete edge cases, partial-failure risks, or traps>
- **Rollout & blast radius:** <Single atomic change vs. staged rollout, deployment windows, approvals, role handoffs, or rollback sensitivity>

## Acceptance Criteria
1. <Observable, deterministic outcome: given <input/state>, <exact output, state change, status code, or error behavior>.>
2. <Observable, deterministic outcome covering key edge or failure case.>
````

## Calibration & Domain Rules

- **Handoff alignment with `implementation-guide`**:
  - `Problem & Goal` + `Scope` feed `Overview`.
  - `Constraints & Decisions` feed `Rationale & Alternatives` (so the guide never invents strawman options).
  - `Grounded Context` + `Risks, Invariants & Rollout` feed `Author Notes & Q&A`, execution guardrails, `Rollback`, and the multi-phase **Split Gate** (explicitly flag stage gates, deployment windows, approvals, or independent subsystem checkpoints whenever they apply).
  - `Acceptance Criteria` feed `Verification` and phase exit gates 1:1.
- **Small / isolated tasks**: Keep proportional. Collapse `Grounded Context` to 2–4 compact bullets if a table adds noise, omit `Prerequisites & Environment` when none apply, and keep `Risks, Invariants & Rollout` to 1–3 high-signal bullets.
- **Non-coding tasks** (infra, ops, security policy, SaaS setup, process): Anchor `Grounded Context` to real hosts, clusters, IAM/DNS/policy objects, dashboards, or exact UI paths (`Settings -> Access -> Policies`). Write `Acceptance Criteria` against observable system checks (DNS record, audit log, API response, permission boundary).

## Gotchas & Anti-Bloat Rules

- **Never write the implementation guide inside the task.** No numbered how-to steps, shell walkthroughs, or solution code—leave execution design to `implementation-guide`.
- **No Agile or PM ceremony.** Never add user stories (*"As a user, I want..."*), story points, priority badges, RACI tables, timelines, or `Summary`/`Conclusion` sections.
- **No vague acceptance criteria.** Ban *"works cleanly"*, *"fast"*, *"well-tested"*, or *"handles errors properly"*. State exact observable outputs, status codes, state transitions, or latency/resource bounds.
- **No ungrounded hand-waving.** Never write *"update the relevant config/service"*—name the exact path, key, or symbol, or mark `[Assumption: ...]`.
- **No filler decisions or risks.** Omit generic advice (*"ensure backward compatibility"*, *"add unit tests"*) unless tied to a specific contract, caller, or failure mode in this system.

## Pre-Delivery Checklist

- [ ] Title states a concrete outcome (`Task: <Action Verb + Specific Outcome>`).
- [ ] Zero step-by-step implementation instructions or solution code (contract snippets only if needed).
- [ ] All paths, symbols, routes, and configs grounded in workspace/docs (or marked `[Assumption]`).
- [ ] `Out of Scope`, `Constraints & Decisions`, and `Risks, Invariants & Rollout` surface real boundaries, stage gates, and rollback needs.
- [ ] Every acceptance criterion is deterministic and directly verifiable.
