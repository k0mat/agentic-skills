# Agentic Skills

Curated collection of zero-bloat [Agent Skills](https://agentskills.io/specification) built for progressive disclosure, deterministic workflows, and clean outputs.

## Available Skills

| Skill | Path | Description |
| :--- | :--- | :--- |
| [`implementation-guide`](skills/implementation-guide/SKILL.md) | `skills/implementation-guide` | Author Baeldung-clarity Markdown implementation guides with upfront rationale, pre-flight author Q&A/guardrails, concrete steps, and verification gates across coding and non-coding tasks. |
| [`input-refiner`](skills/input-refiner/SKILL.md) | `skills/input-refiner` | Refine rough or typo-ridden user input without bloat and ask ≤3 high-ROI clarifying questions only when ambiguity threatens execution quality. |

## Installation

Install with the Skills CLI:

```bash
npx skills add <owner>/agentic-skills --skill implementation-guide
```

Or symlink/copy individual skill directories into your agent's skill path (`.agents/skills/`, `.claude/skills/`, or `~/.agents/skills/`).

## Repository Conventions

- Each skill lives in `skills/<skill-name>/` with `name` matching its directory.
- Keep `SKILL.md` lean (<200 lines target, <500 max); push conditional templates or heavy domain references into `references/` or `assets/` and state exact load triggers.
- Include only instructions an agent would get wrong on its own—no filler, generic explanations, or redundant menus.
