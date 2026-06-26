# Hybrid Architecture Migration — V7 → V7.5

**Date:** 2026-06-26
**Scope:** Worker (V7) and Orchestrator (V73) SOUL templates

## Problem
- Worker template: 368 lines (~15KB) inline in system prompt
- Orchestrator template: 615 lines (~36KB) inline in system prompt
- Result: model instructions simultaneously; updates require full template rewrite

## Solution
Extract 12 procedural protocols into standalone skills under `skills/`:
- `intent-validation`
- `search-protocol`
- `conflict-resolution`
- `tool-use-discipline`
- `reasoning-integrity`
- `anti-hallucination`
- `context-hygiene`
- `human-in-the-loop`
- `execution-provenance`
- `failure-mode-detection`
- `control-plane-management`
- `task-decomposition`

## Metrics
| Metric | V7.4 (inline) | V7.5 (hybrid) | Change |
|--------|---------------|---------------|--------|
| Worker template lines | 368 | 127 | -65% |
| Orchestrator template lines | 615 | 241 | -61% |
| Total skill lines | 0 | 818 | +818 |
| System prompt token overhead | ~51KB | ~13KB | -75% |
| Content preserved | 100% | 100% | 0% loss |

## Key Decision: Flat Skill Structure
Rejected `worker/`, `shared/`, `orchestrator/` subdirectories. Rationale: a skill is a skill; trigger condition determines when it's needed, not folder ownership. The template's `## Mandatory Skill Triggers` table is the only role-differentiation mechanism.

## Enforcement Mechanism
Every template MUST include:
```markdown
## Mandatory Skill Triggers
| When | Skill to Load |
|------|---------------|  | Before X | skill_view(name="Y") |
```

This is NOT optional — without explicit triggers, the agent loads no skill and follows no protocol.

## Validation Method
1. Extract all keywords from each section of original template
2. Search them in (new template + combined skill files)
3. Any missing keyword = content loss = must fix before reporting completion
4. Original templates preserved in `templates/original/` for future validation
