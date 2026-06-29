# Threshold Calibration Pattern — Token-Based Context Triggers

## Pattern

When defining context window triggers in agent skills, use **absolute token counts** rather than **percentages** or **message counts**.

## Rationale

| Approach | Problem |
|----------|---------|
| Message counts | Varies wildly — 5 short messages ≠ 5 long messages in token usage |
| Percentages (e.g., >20%) | Agent cannot self-measure "20% of context used" — non-detectable trigger |
| Token counts (e.g., >50k) | Concrete, machine-detectable when context reporting is available |

## Default Thresholds (for ~128k–200k context window)

| Action | Token Threshold |
|--------|-----------------|
| Proactive distillation | >50,000 tokens |
| Mandatory reset | >75,000 tokens |

## Adjustment by Model Context Limit

| Model Context | Proactive | Mandatory |
|---------------|-----------|-----------|
| 128k | 50k | 75k |
| 200k | 50k | 75k |
| 512k | 100k | 200k |
| 1M | 200k | 500k |

## Real Session Example (2026-06-29)

Migrated `context-hygiene` from `>20%` / `>30%` to `>50k tokens` / `>75k tokens`. Required updating:
- `skills/context-hygiene/SKILL.md` — all trigger references
- `templates/SOUL_TEMPLATE_V7.md` — Mandatory Pre-Action Checks #1, Constraints
- `templates/SOUL_TEMPLATE_ORCHESTRATOR_V76.md` — same
- `SKILL.md` (umbrella) — Skill Inventory table, Pitfall #15

## Rule

Always grep across ALL files after changing a threshold in one place:
```bash
grep -rn "old_threshold\|old_percentage" . --include="*.md"
```

## Pitfall: Forgetting to Update the Umbrella

When updating a flat skill threshold, the `soul-core/SKILL.md` umbrella's Skill Inventory table and Pitfall sections must also reflect the new value. Inconsistent thresholds between the skill and its umbrella cause the agent to receive conflicting instructions depending on which file it reads first.
