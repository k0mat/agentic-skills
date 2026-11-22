# Multi-Phase Implementation Guide Structure

Load and apply this structure **only** when a guide exceeds single-file complexity (>8 major steps, >350 lines, distinct rollout/handoff stages, or blast-radius checkpoints).

## Directory Naming & Layout

```text
<slug>-guide/
├── README.md                  # Global overview, architecture rationale, phase map, final verification
├── phase-01-<phase-slug>.md   # Self-contained phase with its own entry preconditions & exit gate
├── phase-02-<phase-slug>.md
└── ...
```

## Phase Splitting Rules

1. **Split on verifiable state boundaries**, never arbitrary word counts. Every phase must leave the system in a safe, testable state.
2. **Minimize phase count** (typically 2–5 phases). Merge adjacent micro-phases that cannot be verified independently.
3. **Centralize global rationale.** Put cross-cutting architecture decisions and rejected system-level alternatives in `README.md` once; put local tradeoffs inside a phase file only when specific to that phase.
4. **Enforce phase gates.** Phase $N+1$ must explicitly list Phase $N$'s passed gate in its preconditions so work can pause or change owners safely between phases.

---

## `README.md` Template (Guide Index)

````markdown
# How to <Action Verb + Overall Outcome>

## Overview
<2-4 sentences: problem solved, end-to-end target state, and explicit out-of-scope boundary.>

## Rationale & Alternatives
<Why this overall architecture and phased sequence were chosen.>

| Alternative | Why Rejected |
| :--- | :--- |
| <System-level option A> | <Concrete tradeoff, risk, or constraint violation> |

## Global Prerequisites
- <Access, tooling, environments, or approvals needed across the effort>

## Phase Plan

| Phase | Guide | Deliverable | Exit Gate |
| :--- | :--- | :--- | :--- |
| 1 | [Phase 1: <Title>](phase-01-<slug>.md) | <Concrete artifact/state> | <1-line verification check> |
| 2 | [Phase 2: <Title>](phase-02-<slug>.md) | <Concrete artifact/state> | <1-line verification check> |

<Include a `mermaid` graph ONLY when phases branch or can run in parallel; omit for strictly linear 1 -> 2 -> 3 sequences.>

## End-to-End Verification
1. <Final cross-phase integration or smoke check after all phases complete> — **Expected:** <observable end state>.
````

---

## `phase-NN-<phase-slug>.md` Template

````markdown
# Phase <N>: <Action Verb + Phase Deliverable>

Parent: [Overview](README.md) | Previous: [Phase <N-1>](phase-0<N-1>-<slug>.md) | Next: [Phase <N+1>](phase-0<N+1>-<slug>.md)
*(Omit Previous on Phase 1; omit Next on final phase.)*

## Scope & Preconditions
<1-2 sentences defining this phase's exact deliverable.>

- **Required prior state:** <Specific artifact or passed gate from Phase N-1, or initial system state for Phase 1>

## Implementation

### 1. <Action Verb + Concrete Target>
<1-2 sentences stating what this step achieves.>

```<lang>
# Target: <exact file path, host, or UI location>
<complete snippet, minimal diff, command, query, or ordered actions>
```

> **Gotcha:** <Include ONLY when a non-obvious trap would break execution.>

### 2. <Action Verb + Concrete Target>
...

## Phase Verification Gate
*Do not start Phase <N+1> until every check below passes.*

1. <Exact command, query, or check> — **Expected:** <specific output or state>.

## Phase Rollback
<Include ONLY if this phase mutates state. Exact steps to revert Phase N cleanly back to Phase N-1's verified state.>
````
