---
name: decision-gate
description: >
  Escalate major, hard-to-reverse architectural, product, contract, or operational
  decisions to the operator with 2-4 viable options scored on grounded Quality
  and Effort metrics plus a recommended default. Use this skill when planning or
  executing reveals a substantive fork (schema, public API, persistence, auth,
  new dependency/infra, UX direction, or multi-subsystem rework) not settled by
  repo conventions. Do NOT trigger on small or easily reversible implementation
  details (naming, helper layout, local refactors), clear single-winner choices,
  or garbled/underspecified prompts (use `input-refiner` for front-door clarity).
---

# Decision Gate

Surface high-impact forks before committing to costly or hard-to-reverse work. Decide small, reversible details autonomously; escalate only decisions whose tradeoffs genuinely belong to the operator.

## Workflow

### 1. Run the Escalation Gate
Inspect repo conventions, configs, and target files in **one bounded read-only pass**, then pause to ask the operator **only when all three hold**:
1. **High blast radius or lock-in**: touches a public API/CLI/schema contract, persistent data model, auth/security boundary, new runtime dependency/infra, destructive migration, divergent user workflow, or cross-subsystem rework.
2. **Unsettled by existing conventions**: codebase patterns, architecture docs, and stated constraints do not already dictate a single idiomatic path.
3. **Genuine multi-option fork**: `>=2` distinct, viable paths exist with real tradeoffs.

If any check fails (local/reversible choice, standard repo pattern applies, or one clear winner): **exit this skill silently** and execute directly.

### 2. Generate & Prune Options (2–4 Max)
- Draft **2–4 genuinely distinct options** (different ownership, data flow, contract, or boundary—never cosmetic variants).
- **Drop dominated options**: if Option A matches or beats Option B on both **Quality** and **Effort** with no distinct reversibility or risk upside, eliminate Option B.
- If pruning leaves **1 option**, exit silently and execute it.

### 3. Score With Anchored Metrics
Score every surviving option using the `1–5` scales below, backed by a short workspace-grounded reason (never bare numbers):

| Metric | 1 | 3 | 5 |
| :--- | :--- | :--- | :--- |
| **Quality** *(fit, maintainability, risk)* | Brittle hack; high defect or lock-in risk | Sound compromise; localized debt | Clean long-term fit; low failure surface |
| **Effort** *(scope, migration, tests)* | Isolated change; minimal verification | Multi-file change + contract/test updates | Cross-system rework, migration, or new infra |

Also tag **Reversibility**:
- `Easy` — behind flag/config or isolated module
- `Moderate` — refactor across several callers, no persistent state impact
- `Hard` — schema/data migration, public contract, external dependency, or UX lock-in

### 4. Recommend & Present
- Always designate **1 Recommended** option and state the decisive reason in 1 sentence so the operator can approve in one click or word.
- **Prefer native form UI**: when the harness provides a structured prompt tool (e.g., `ask` / `AskUserQuestion`), put the brief decision context + scored table inside the form and preselect the recommended option. Otherwise output the compact Markdown format below.
- After the operator answers, execute their decision immediately without re-litigating.

## Output Format (Markdown Fallback)

````markdown
**Decision needed:** <1-sentence statement of the fork and why it is hard to reverse.>

| Option | Quality (1–5) | Effort (1–5) | Reversibility | Tradeoff & Evidence |
| :--- | :--- | :--- | :--- | :--- |
| **A: <Short name>** *(Recommended)* | `<score>/5` | `<score>/5` | `<Easy/Mod/Hard>` | <Concrete upside, downside, and scope/file anchor> |
| **B: <Short name>** | `<score>/5` | `<score>/5` | `<Easy/Mod/Hard>` | <Concrete upside, downside, and scope/file anchor> |

**Recommendation:** Option A — <1-sentence rationale>. Reply with `A`, `B`, or adjustments.
````

*(If using a native form tool, omit the duplicate Markdown table from chat and render the comparison inside the form.)*

## Gotchas & Anti-Bloat Rules

- **Never escalate small details.** Variable names, private helper splits, internal error formatting, and reversible local refactors are yours to decide.
- **Zero strawman options.** Never invent a weak third option just to fill a table; 2 strong options beat 3 padded ones.
- **No unanchored scores.** Always tie Quality/Effort ratings to concrete evidence (caller count, migration, dependency, failure mode).
- **Do not overlap `input-refiner`.** If the user's request is garbled or missing basic requirements, clarify intent first (`input-refiner`) rather than inventing architectural options around a misunderstood ask.
- **Respect overrides cleanly.** If the operator picks a higher-effort or lower-scored option, execute it directly without lecturing.
