# soul-core Repository Structure Reference

**Source:** Local clone at `/home/degi/project/soul-core` (verified 2026-06-30)
**Git remote:** `degidevops/soul-core` (private/not found on public GitHub)
**Version:** V7.6.4

## Directory Layout

```
soul-core/
├── SKILL.md                          # Umbrella skill (285 lines, 16 skill inventory)
├── skills/                           # 16 flat skills (no subfolders)
│   ├── anti-hallucination/SKILL.md
│   ├── conflict-resolution/SKILL.md
│   ├── context-hygiene/SKILL.md
│   ├── control-plane-management/SKILL.md   # Orchestrator-only
│   ├── execution-provenance/SKILL.md
│   ├── failure-mode-detection/SKILL.md
│   ├── human-in-the-loop/SKILL.md
│   ├── intent-validation/SKILL.md
│   ├── memory-lifecycle/SKILL.md
│   ├── model-routing/SKILL.md
│   ├── reasoning-integrity/SKILL.md
│   ├── rollback-revert/SKILL.md
│   ├── search-protocol/SKILL.md
│   ├── skill-orchestration/SKILL.md
│   ├── task-decomposition/SKILL.md         # Orchestrator-only
│   └── tool-use-discipline/SKILL.md
├── templates/
│   ├── SOUL_TEMPLATE_V7.md                 # Worker (163 lines)
│   ├── SOUL_TEMPLATE_ORCHESTRATOR_V76.md   # Orchestrator (230 lines)
│   └── original/                            # Preserved originals
│       ├── SOUL_TEMPLATE_V7.md
│       └── SOUL_TEMPLATE_ORCHESTRATOR_V76.md
└── references/
    ├── CHANGELOG.md                         # V7.2.1 → V7.6.4
    ├── SOUL_TEMPLATE_V7_REFERENCE.md
    ├── SOUL_TEMPLATE_ORCHESTRATOR_V73_REFERENCE.md
    ├── hybrid-architecture-enforcement.md
    ├── hybrid-architecture-migration.md
    ├── in-place-migration-recipe.md
    ├── profile-clone-behavior.md
    ├── threshold-calibration-pattern.md
    └── v764-audit-fix-notes.md
```

## Key Facts

| Metric | Value |
|--------|-------|
| Total skills | 16 (12 procedural + 2 meta + 2 orchestrator-only) |
| Worker template lines | 163 |
| Orchestrator template lines | 230 |
| Reference docs | 9 |
| License | MIT |
| Replaces | `degidevops/soul-management` (archived 2026-06-23) |

## Evolution Timeline

1. **V7.3** — Monolithic templates (all protocol inline)
2. **V7.4** — XML→Markdown refactor, dynamic context hygiene
3. **V7.5** — Hybrid architecture (inline identity + 12 flat skills), flat structure (no subfolders)
4. **V7.6** — Numbered mandatory pre-action checks replace passive trigger table
5. **V7.6.4** — Threshold calibration (tokens not %), audit fixes, search-protocol binary provenance

## Relationship to Other degidevops Repos

| Repo | Status | Purpose |
|------|--------|---------|
| `soul-engine` | Public | Earlier modular template system (5 core skills) |
| `soul-management` | Archived | Predecessor to soul-core |
| `soul-gen` | Deleted/404 | Was SOUL profile generator |
