# V7.6.4 Audit Fix Notes — soul-core Post-Mortem

**Date:** 2026-06-29
**Trigger:** Full audit of soul-core v7.6 revealed 10 inconsistencies across templates, skills, and reference files.

## What Was Fixed

### 1. Orchestrator Template Rename (V73 → V76)
- `templates/SOUL_TEMPLATE_ORCHESTRATOR_V73.md` → `_V76.md`
- `templates/original/SOUL_TEMPLATE_ORCHESTRATOR_V73.md` → `_V76.md`
- Updated 9 files referencing the old name

### 2. Threshold Synchronization
- Migrated remaining `>20%` / `>30%` references to `>50,000 tokens` / `>75,000 tokens`
- Files patched: `in-place-migration-recipe.md`, `SOUL_TEMPLATE_V7_REFERENCE.md`, `SOUL_TEMPLATE_ORCHESTRATOR_V73_REFERENCE.md`

### 3. "Step N" Removal from Skill Titles
- 8 skills still had "Step N:" in H1 title after V7.5 extraction
- Fixed: `task-decomposition`, `reasoning-integrity`, `human-in-the-loop`, `execution-provenance`, `conflict-resolution`, `intent-validation`, `control-plane-management`, `anti-hallucination`

### 4. Backend-Agnostic Extraction Hierarchy
- `search-protocol` had SearXNG-specific tools hardcoded in extraction hierarchy
- Changed to generic: `web_search` → `web_extract` → `[Backend precision tool]` → `[Backend full HTML tool]`
- SearXNG tools (camofox_*) now documented as backend-specific examples, not requirements

### 5. PARTIAL Status Added to execution-provenance
- Was inconsistent with template and search-protocol (3 vs 4 statuses)
- Added: **PARTIAL:** One source found, limited corroboration, or value partially matches
- Updated compliance gate: "PARTIAL claims must be flagged as 'PARTIAL — limited corroboration' in delivery"

### 6. rollback-revert Trigger Added to Templates
- Was in inventory but had no trigger entry in Mandatory Pre-Action Checks
- Added as sub-trigger in check #7 (Destructive action): "rollback/reversal" → also load `rollback-revert`

### 7. model-routing Removed from Umbrella
- User decision: too niche for mandatory trigger
- Removed from umbrella SKILL.md inventory table and related_skills frontmatter
- Skill file itself remains in `skills/model-routing/` for optional use

### 8. CHANGELOG Reorder
- V7.6.0 section was placed below V7.2.1 (non-chronological)
- Reordered: V7.6.3 → V7.6.2 → V7.6.1 → V7.6.0 → V7.5.0 → ...

### 9. AGENT_DESCRIPTION Duplex Fix
- Orchestrator template had hardcoded suffix after `{{AGENT_DESCRIPTION}}` placeholder
- Removed suffix to prevent duplication when user fills placeholder

### 10. intent-validation Precision → Clarity Criteria
- Replaced non-measurable "precision ≥ 70%" with concrete criteria:
  - HIGH: ≥1 action verb + ≥1 target object + domain context clear
  - MEDIUM: missing one element
  - LOW: <3 words, topic drop, or conflicting intents

## Lessons Learned

1. **search_files truncation:** Always re-scan with higher limit when auditing directories with >50 files
2. **Incomplete first-pass fixes:** "Step N" was partially fixed in V7.6.2 (tool-use-discipline only). Always grep across ALL skills after a rename/cleanup operation.
3. **Skill vs template consistency:** When removing a skill from umbrella, check: inventory table, related_skills frontmatter, template trigger checks, and any cross-references in other skills.
4. **Backend-agnostic shared skills:** Skills used by both worker and orchestrator must not hardcode backend-specific tool names in their primary protocol. Use `[Backend tool]` notation with examples.

## Verification Checklist Used

```
grep -rn "ORCHESTRATOR_V73" . --include="*.md" | grep -v PLAN | grep -v REFERENCE
grep -rn "Step [0-9]:" skills/ --include="*.md"
grep -rn ">20%|>30%" . --include="*.md" | grep -v original | grep -v CHANGELOG | grep -v threshold-calibration
grep -n "model-routing" SKILL.md
grep -n "rollback-revert" templates/SOUL_TEMPLATE_V7.md
grep -n "rollback-revert" templates/SOUL_TEMPLATE_ORCHESTRATOR_V76.md
grep -n "PARTIAL" skills/execution-provenance/SKILL.md
grep -n "^## \[v" references/CHANGELOG.md
```
