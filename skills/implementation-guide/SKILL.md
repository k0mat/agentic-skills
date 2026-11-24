---
name: implementation-guide
description: >
  Author clear, self-contained Markdown implementation guides with rationale,
  considered alternatives, actionable step-by-step instructions, and concrete
  verification checks across coding, infrastructure, operations, and process
  tasks. Use this skill whenever the user asks for an implementation guide,
  how-to guide, rollout/migration playbook, feature build-out guide, or
  Baeldung-style technical walkthrough—even if they just say "write a guide on
  how to set up/implement X". Do NOT trigger when the user wants you to
  directly execute the change rather than produce a guide document.
---

# Implementation Guide Authoring

Produce concise, executable Markdown guides that separate **upfront decision context** (overview, rationale, rejected alternatives), **pre-flight author alignment** (specialist persona, role-tiered Q&A, execution guardrails), and **uninterrupted execution flow** (numbered action steps + verification). A competent reader or agent must be able to execute Step 1 through Verification linearly without guessing paths, hunting for context, or wading through filler.

## Workflow

### 1. Ground Before Drafting
- Inspect relevant workspace files, configs, schemas, or existing docs in one bounded pass; limit external doc lookups to 1–2 targeted checks for uncertain APIs or flags rather than exhaustive manual crawling.
- Reference real file paths, symbol names, CLI flags, API routes, or UI locations from the target system.
- When external systems cannot be inspected, list unverifiable premises explicitly under `## Prerequisites` (`[Assumption: <concise premise>]`)—never fabricate flags, endpoints, or settings.
- Output path: honor any user-specified path; otherwise follow existing repo docs **directory/naming** conventions (e.g., `docs/guides/<slug>.md`) or default to `./<slug>-guide.md`. Match path conventions only—never copy monolithic structure from unsplit legacy guides.

### 2. Run the Complexity & Split Gate
Use a **single file** (`<slug>-guide.md`) only when **none** of the split thresholds below hold.

**MUST split** into a multi-phase directory (`<slug>-guide/README.md` + `phase-NN-<slug>.md`) when **at least one** threshold holds:
- **Scale**: `>8` major implementation steps, **or** `>350` lines of Markdown **excluding fenced code blocks** (count headings, prose, lists, and tables only—large inline code, SQL, or config blocks do not count toward the 350-line limit, and `Author Notes & Q&A` alone never justifies splitting).
- **Stage gates**: Separate deployment windows, approvals, or role handoffs (e.g., schema migration -> service rollout -> cutover).
- **Blast radius**: Independent subsystems that benefit from pausing at verified intermediate checkpoints.

**Legacy precedent override:** Ignore historical unsplit guides in the workspace when evaluating this gate. Even if older guides are single-file monoliths, split whenever any threshold above triggers.

If splitting, **read [`references/multi-phase-template.md`](references/multi-phase-template.md)** before drafting.

### 3. Draft with the Single-File Structure

Adapt this template to the task:

````markdown
# How to <Action Verb + Specific Outcome>

## Overview
<2-4 sentences: problem solved, target end state, and explicit out-of-scope boundary.>

## Rationale & Alternatives
<Concise explanation of why the chosen approach fits current constraints.>

<!-- Include table ONLY when ≥2 genuinely viable options exist; never list options already ruled out by hard user constraints. Otherwise rename heading to "## Rationale" and omit table. -->
| Alternative | Why Rejected |
| :--- | :--- |
| <Viable option A> | <Concrete tradeoff, failure mode, or constraint violation> |

## Prerequisites
- <Non-obvious access, state, versions, secrets, or prior artifacts required before Step 1>

## Author Notes & Q&A

**Author Perspective:** <Specialist role & seniority most suited to own this task, e.g., Staff SRE / Senior Backend Engineer> — <1-sentence operational lens or primary risk focus>.

### Junior <Dev / Operator> Q&A
1. **Q:** <Project-specific execution, wiring, or state-invariant question—never generic syntax/tool basics>
   **A:** <1-3 sentences referencing real files, flags, or step dependencies.>
2. **Q:** ...
   **A:** ...
3. **Q:** ...
   **A:** ...

### Mid-Level <Dev / Operator> Q&A
1. **Q:** <Edge case, concurrency/idempotency, partial-failure recovery, or alternative-boundary question>
   **A:** <1-3 sentences stating exact behavior, invariant, or tradeoff.>
2. **Q:** ...
   **A:** ...
3. **Q:** ...
   **A:** ...

### Author Execution Guardrails
1. <Imperative rule on step ordering, blast radius, or state invariant>
2. <Imperative rule on what NOT to skip, batch, or improvise>
3. <Imperative rule on verification or rollback boundary>

## Implementation

### 1. <Action Verb + Concrete Target>
<1-2 sentences stating what this step achieves and where it applies.>

```<lang>
# Target: <exact file path, host, or UI navigation path>
<complete snippet, minimal anchored diff, command, query, or ordered UI/ops actions>
```

> **Gotcha:** <Include ONLY when a non-obvious trap would break execution.>

### 2. <Action Verb + Concrete Target>
...

## Verification
1. <Exact command, request, query, or inspection> — **Expected:** <specific output, status code, or observable state>.

## Rollback
<Include ONLY when steps mutate live state, schemas, permissions, or infrastructure. Exact revert commands/actions.>
````

## Calibration & Domain Rules

- **Conditional `Considered Alternatives`**: Include the table only when ≥2 viable designs exist. When hard constraints dictate a single path, rename the heading to `## Rationale`, state the constraint in 1–2 sentences, and omit the table—never invent strawman options.
- **Non-coding tasks** (ops, infra, security policy, SaaS setup, team workflows): Replace code blocks with the task's native executable artifact—CLI commands, DNS/IAM records, SQL/API payloads, config tables, or exact UI paths (`Settings -> Access -> Create Policy`). Keep `Verification` deterministic (e.g., `dig` response, audit event, test login).
- **Artifact sizing**: Include enough anchor context (file path, imports, parent key, function signature) that placement is unambiguous. Use full blocks for new files/configs; use minimal context or `diff` blocks when editing large existing files instead of dumping untouched lines.
- **Diagrams**: Add a `mermaid` diagram only for non-trivial data flow, state transitions, or multi-service sequences. Never diagram linear 3-step lists.
- **Author Notes & Q&A calibration**:
  - State **Author Perspective** functionally (*role + decision/risk lens*)—never resume or years-of-experience bio prose.
  - Adapt `Dev` vs. `Engineer` / `Operator` / `Admin` labels to the task domain.
  - Target **3 Junior Q&As, 3 Mid-Level Q&As, and 3 Execution Guardrails**. On trivial `≤3`-step tasks, drop to 1–2 Junior/Mid Q&As rather than inventing generic filler. Every Q&A must reference this task's actual files, flags, invariants, or failure modes.

## Gotchas & Anti-Bloat Rules

- **No `Conclusion`, `Summary`, or `What We Learned` sections.** End immediately after `Verification` (or `Rollback`).
- **Keep explanation out of steps.** Put architectural *why* in `Rationale & Alternatives`, execution *why*/edge-case questions in `Author Notes & Q&A`, and keep `Implementation` strictly to *what to do*, the artifact, and critical inline gotchas.
- **Assume baseline competence.** Never explain language syntax, standard CLI flags, or basic concepts (what JWT, cron, Docker, or webhooks are). Explain only project-specific invariants and traps.
- **No `TODO` or placeholder holes.** Every step must be actionable as written. If user-specific values are required, use explicit named placeholders (`$PROD_DB_URL`, `<AWS_ACCOUNT_ID>`) and list them in `Prerequisites`.
- **Zero synthetic Q&A filler.** Never ask or answer textbook basics (*"What is a webhook?"*, *"Why write tests?"*) or generic platitudes (*"Monitor logs carefully"*).

## Pre-Delivery Checklist

- [ ] Title begins with `How to` + concrete outcome.
- [ ] All paths, flags, and identifiers grounded in workspace/docs (or marked `[Assumption]`).
- [ ] Split gate evaluated on `>8` steps / `>350` non-code Markdown lines / stage gates, ignoring unsplit legacy guides.
- [ ] `Author Notes & Q&A` contains a functional persona, grounded Junior/Mid Q&As (zero textbook filler), and 3 execution guardrails.
- [ ] Linear flow: Step $N$ never depends on context introduced in Step $N+1$.
- [ ] Zero summary prose, strawman alternatives, or tutorial boilerplate.
