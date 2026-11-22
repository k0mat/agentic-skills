---
name: input-refiner
description: >
  Refine rough, typo-ridden, or underspecified user input into clear, concise
  instructions and ask targeted clarifying questions before costly execution.
  Use this skill ONLY when (1) the user explicitly asks to refine, rewrite, fix,
  or sharpen a prompt, request, or spec, OR (2) an incoming multi-step or
  high-blast-radius task has garbled phrasing, contradictions, or missing
  constraints where guessing risks wasted rework. Do NOT trigger on clear,
  actionable requests—even if they contain harmless typos—or on trivial lookups,
  single-step fixes, or follow-up replies where refinement costs more tokens
  than executing directly.
---

# Input Refinement & Clarification

Turn rough, garbled, or underspecified input into crisp requirements and resolve high-impact ambiguities with minimal token spend. Intervene only when tightening or clarifying the input saves more rework than the extra tokens and round-trip cost.

## Workflow

### 1. Run the Token-ROI Gate
Evaluate whether intervention improves work quality before emitting anything:
- **Explicit refinement** (user asks to rewrite, fix, clean up, or sharpen a prompt/spec): Proceed to Step 2.
- **Task execution with blocking ambiguity** (garbled dictation/grammar creates ≥2 plausible meanings, requirements conflict, or missing scope/architecture choices risk costly rework or destructive changes): Proceed to Step 2.
- **Clear intent despite surface mistakes** (typos, ESL phrasing, or shorthand where target and outcome are unambiguous, e.g., *"plz fix teh off by 1 in paginate.ts"*): **Exit this skill silently** (`0` extra output tokens) and execute the task directly.

### 2. Ground Before Asking (One Bounded Check)
Never ask the user what workspace context answers cheaper:
- Run at most **one fast read-only pass** (target files, config, schema, or recent history) to resolve shorthand references, stack conventions, and existing patterns.
- Drop any candidate question already settled by the codebase, and use discovered conventions to preselect defaults for remaining questions.

### 3. Fix Writing Without Bloat
When rewriting input (for an explicit rewrite request, or to restate garbled intent before asking):
- **Fix mechanics**: Correct spelling, grammar, punctuation, speech-to-text errors, tangled clauses, and ambiguous pronouns (`it`, `that`, `there`, `both`) in the user's language.
- **Lock technical tokens**: Preserve exact file paths, symbol names, CLI flags, error strings, numbers, and hard constraints verbatim.
- **Keep length ≤ original**: Compress wordy or repetitive phrasing into direct prose or flat bullets. Add bullet structure only when splitting tangled multi-part asks improves scan speed.
- **Zero prompt-engineering fluff**: Never inject persona preambles (*"Act as a senior..."*), flattery, generic quality filler (*"ensure clean, scalable, maintainable code"*), or unrequested scope.

### 4. Clarify Only Decision-Shaping Gaps
Every clarification round replays full conversation context—spend a turn only when guessing risks real rework:
- **Reversible / low-risk gap**: Do **not** pause. State a 1-line assumption anchored in repo conventions and execute immediately.
- **High-blast-radius gap** (public API/schema contract, data mutation/deletion, conflicting constraints, or ≥2 divergent designs): Pause and ask **1–3 questions maximum**, ordered by impact.
- **Concrete choices + default**: Frame each question as 2–4 concrete options and mark the recommended default so the user can answer in one word or click.
- **Prefer native form UI**: When the harness provides a structured prompt tool (e.g., `ask` / `AskUserQuestion`), present options there with defaults preselected instead of prose walls; otherwise use a compact numbered list.

## Output Formats

### Mode A: Explicit Input / Prompt Rewrite
Output the tightened text directly—no meta-commentary, diff walkthrough, or grammar lecture. Append open questions **only** if critical gaps remain:

````markdown
<Corrected, tightened prompt or specification>

<!-- Omit when self-contained -->
**Clarify if needed:**
1. **<Decision>**: <Option A (Recommended)> or <Option B>?
````

### Mode B: Pre-Execution Clarification
Restate the cleaned-up intent in 1–2 sentences so the user sees their rough input was understood, then ask only the blocking question(s). *(If using a structured question tool, place the **Intent** line inside the form context instead of duplicating questions in chat.)*

````markdown
**Intent:** <1-2 sentence corrected, unambiguous summary of goal and scope.>

1. **<Decision>** — <Option A (Recommended: matches `existing/pattern`)> or <Option B>?
2. **<Decision>** — <Option A (Recommended)> or <Option B>?
````

## Gotchas & Anti-Bloat Rules

- **Never echo-rewrite clear execution tasks.** Printing *"Here is a polished version of your request..."* before executing a straightforward task wastes output tokens for zero quality gain.
- **No changelog of writing mistakes.** Repair spelling and grammar silently inside the rewritten prompt or `Intent` summary; never list what typos were fixed.
- **No speculative interrogations.** Never dump 5–10 edge-case questions (*"What about retries, rate limits, logging, i18n...?"*). Ask ≤3 blocking decisions; apply standard repo defaults to the rest.
- **Never "correct" user intent.** Fixing writing means clarifying expression, not silently overriding constraints, stack choices, or acceptance criteria the user chose.
