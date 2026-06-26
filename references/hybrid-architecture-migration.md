# Hybrid Architecture Migration — V7.4 → V7.5

**Date:** 2026-06-26  
**Trigger:** Triangulation research on best system prompt structure for Hermes Agent

## Key Decision: Hybrid (Inline Identity + Skill-Loaded Protocols)

### Problem
- Full V7 template inline = ~200 lines (Worker), ~400 lines (Orchestrator)
- At these sizes, agent compliance degrades — too much for the model to consistently follow
- All detail in one file = hard to maintain, update, or reuse across profiles

### Solution
- **SOUL.md** = Identity + Guardrails + Mandatory Skill Triggers (compact, ~127 lines for Worker)
- **Skills** = Full procedural protocols loaded via `skill_view(name="...")` on trigger
- Agent sees compact SOUL.md at all times (guaranteed compliance with anchors)
- Protocol detail loaded only when needed (progressive disclosure)

### Critical Design Rule: Mandatory Skill Triggers
Without explicit trigger instructions in SOUL.md, the agent has no way to know WHICH skill to invoke WHEN. The trigger table is the enforcement mechanism:

```
Before [ACTION] → load `skill_view(name="skill-name")`
```

Omitting triggers = agent may skip protocols entirely. This was the user's core correction.

### Flat Skill Structure
Skills are NOT separated into `worker/`, `shared/`, `orchestrator/` subfolders. Rationale:
- `search-protocol` is equally relevant to workers and orchestrators
- Trigger condition in the template determines relevance, not folder ownership
- Category subfolders add maintenance overhead with no functional benefit

### Content Extraction Rule
When extracting protocols from original templates into skills, extract VERBATIM. Paraphrasing from memory loses critical detail:
- Anchor re-injection definitions (4 standard + orchestrator-specific variants)
- Full failure mode tables (8 worker + 5 orchestrator-specific)
- Dispatcher coordination rules
- Instruction contamination guard
- Semantic grounding and evidence ranking
- PROCEED_SIGNAL security scope

Original templates are preserved in `templates/original/` as validation reference.

### Metrics
| Aspek | Before (V7.4) | After (V7.5) |
|-------|----------------|---------------|
| Worker SOUL.md | ~200 lines | ~127 lines |
| Orchestrator SOUL.md | ~400 lines | ~241 lines |
| Skill files | 0 | 12 proper SKILL.md |
| Token overhead | ~5-8KB per session | ~2-3KB per session (lazy-load) |
| Maintainability | Rewrite whole template | Update skill independently |
