---
name: soul-management
description: "Manage worker (V7.5) and orchestrator (V7.5) SOUL.md templates with hybrid architecture. All protocols live as flat skills; SOUL.md carries identity + mandatory skill triggers."
version: 7.5.0
author: Maru (soul-gen project)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    category: soul-core
    tags: [soul, templates, profiles, orchestrator, agentic]
    related_skills: [hermes-agent, triangulation-research]
---

# SOUL & Agentic Engineering Management

This skill manages SOUL.md template profiles for Hermes Agent using a **hybrid architecture**: SOUL.md carries identity + mandatory skill triggers, while detailed protocols are loaded via `skill_view` when triggered.

## Architecture: Hybrid (Inline Identity + Flat Skills)

| Layer | Content | Location |
|-------|---------|----------|
| **SOUL.md** | Identity, scope, guardrails, mandatory skill triggers, escalation anchors | System prompt (always loaded) |
| **Skills** (flat) | 12 procedural protocols — each in `skills/<name>/SKILL.md` | Loaded via `skill_view(name="...")` on trigger |

**Rationale for flat structure:** A skill is a skill. `search-protocol` is equally relevant to workers and orchestrators. Category subfolders (`worker/`, `shared/`, `orchestrator/`) add no value — the trigger condition in SOUL.md determines when each skill is needed, not who "owns" it.

## Mandatory Skill Triggers in Template

Every SOUL.md template MUST include a `## Mandatory Skill Triggers` section with explicit `skill_view(name="...")` calls:

```
Before [ACTION] → load `skill_view(name="skill-name")`
```

## Skill Inventory (12 skills, flat)

| Skill | Trigger Condition | Profile |
|-------|-------------------|---------|
| `intent-validation` | Before task with unclear scope or intent < 70% | Both |
| `search-protocol` | Before any factual claim from external data | Both |
| `conflict-resolution` | When external sources contradict or conflict | Both |
| `tool-use-discipline` | Before tool call in multi-turn or multi-step context | Both |
| `reasoning-integrity` | Before multi-step analysis or chain of thought | Both |
| `anti-hallucination` | Before delivering ANY final answer | Both |
| `context-hygiene` | When context >20% or after compression | Both |
| `human-in-the-loop` | Before destructive/irreversible/high-stakes action | Both |
| `execution-provenance` | When emitting trace events or audit trail | Worker |
| `failure-mode-detection` | When anomalies or repeated failures detected | Both |
| `control-plane-management` | Before orchestration decision involving task state | Orchestrator |
| `task-decomposition` | When decomposing goals into atomic tasks | Orchestrator |

## When to Use
- Creating, deploying, or verifying a Worker or Orchestrator profile
- Auditing existing SOUL.md for skill coverage
- Restructuring templates for token efficiency

## Procedure

### 1. Template Selection
- **Worker** → `templates/SOUL_TEMPLATE_V7.md`
- **Orchestrator** → `templates/SOUL_TEMPLATE_ORCHESTRATOR_V73.md`

### 2. Deployment Workflow

1. Read template in full.
2. Read relevant `skills/<name>/SKILL.md` files to understand coverage.
3. Replace `{{PLACEHOLDER}}` values with profile-specific content.
4. Include ALL mandatory skill triggers in the template.
5. Deploy via `write_file` to `~/.hermes/profiles/<name>/SOUL.md` or `~/.hermes/SOUL.md`.
6. Post-deployment validation.

## Verification
1. No `{{` or `}}` placeholders remain.
2. No HTML comments remain.
3. Every skill has a corresponding trigger in the template.
4. All skill `name:` values match their `SKILL.md` frontmatter exactly.
5. Section C has concrete content.

## Pitfalls

### 1. Skill Name Mismatch
Trigger references in templates MUST match the exact `name:` field in the target SKILL.md's YAML frontmatter. A typo = agent fails to load the protocol.

### 2. Improper Skill Format
Skills MUST be proper Hermes-format: `<name>/SKILL.md` with YAML frontmatter (`name`, `description`, `version`, `author`, `license`, `platforms`, `metadata.hermes.*`) + body with canonical section order: `## When to Use` → `## Procedure` → `## Pitfalls` → `## Verification`. Plain `.md` files without the folder + SKILL.md wrapper are NOT valid skills and will not be discovered by `skill_view`.

### 3. Artificial Category Subfolders
Do NOT create `worker/`, `shared/`, `orchestrator/` subfolders inside the skills directory. A skill is a skill — its trigger condition in the template determines when it's needed, not who "owns" it. Flat structure (`skills/<name>/SKILL.md`) is correct. The only role-differentiation lives in the template's trigger table.

### 4. Paraphrased Skill Content
When extracting protocols from an existing template into skills, extract VERBATIM from the original source. Paraphrasing from memory loses critical detail (anchor definitions, full failure modes, dispatcher coordination rules, instruction contamination guard, etc.). Always validate against the original template stored in `templates/original/`.

### 5. Missing Original Validation
After restructuring, always diff against `templates/original/` to verify no content was lost. Original templates MUST be preserved as reference copies.

## References
- `templates/SOUL_TEMPLATE_V7.md` — Worker template
- `templates/SOUL_TEMPLATE_ORCHESTRATOR_V73.md` — Orchestrator template
- `references/SOUL_TEMPLATE_V7_REFERENCE.md` — Worker rationale & citations
- `references/SOUL_TEMPLATE_ORCHESTRATOR_V73_REFERENCE.md` — Orchestrator rationale & citations
- `references/CHANGELOG.md` — Version history
- `references/hybrid-architecture-migration.md` — V7.4→V7.5 migration rationale and metrics
