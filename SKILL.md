---
name: soul-core
description: "Manage worker (V7.6) and orchestrator (V7.6) SOUL.md templates with hybrid architecture. All protocols live as flat skills; SOUL.md carries identity + mandatory pre-action checks."
version: 7.6.0
author: Maru (soul-gen project)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    category: soul-core
    tags: [soul, templates, profiles, orchestrator, agentic]
    related_skills: ["anti-hallucination", "conflict-resolution", "context-hygiene", "control-plane-management", "execution-provenance", "failure-mode-detection", "human-in-the-loop", "intent-validation", "memory-lifecycle", "model-routing", "reasoning-integrity", "rollback-revert", "search-protocol", "skill-orchestration", "task-decomposition", "tool-use-discipline"]
---

# SOUL & Agentic Engineering Management

This skill manages SOUL.md template profiles for Hermes Agent using a **hybrid architecture**: SOUL.md carries identity + mandatory pre-action checks, while detailed protocols are loaded via `skill_view` when triggered.

## Architecture: Hybrid (Inline Identity + Flat Skills)

| Layer | Content | Location |
|-------|---------|----------|
| **SOUL.md** | Identity, scope, guardrails, mandatory pre-action checks, escalation anchors | System prompt (always loaded) |
| **Skills** (flat) | 12 procedural protocols — each in `skills/<name>/SKILL.md` | Loaded via `skill_view(name="...")` on trigger |

**Rationale for flat structure:** A skill is a skill. `search-protocol` is equally relevant to workers and orchestrators. Category subfolders (`worker/`, `shared/`, `orchestrator/`) add no value — the trigger condition in SOUL.md determines when each skill is needed, not who "owns" it.

## Mandatory Pre-Action Checks in Template

Every SOUL.md template MUST include a `## Mandatory Pre-Action Checks` section with numbered imperative sequence:

```
1. [Condition] → `skill_view(name="skill-name")` FIRST
2. [Condition] → `skill_view(name="skill-name")` FIRST
...
N. **Pre-delivery gate:** BEFORE delivering final answer → `skill_view(name="anti-hallucination")` FIRST
```

## Skill Inventory (12 skills, flat)

| Skill | Trigger Condition (Machine-Detectable) | Profile |
|-------|----------------------------------------|---------|
| `intent-validation` | User request has ambiguous scope/goal/constraints | Both |
| `search-protocol` | Response will contain ANY factual claim | Both |
| `conflict-resolution` | After search returning >1 source, before using results | Both |
| `tool-use-discipline` | This is SECOND or subsequent tool call in session | Both |
| `reasoning-integrity` | Conclusion requires combining evidence from >1 source or >1 inference step | Both |
| `anti-hallucination` | BEFORE delivering ANY final answer to user | Both |
| `context-hygiene` | Session has ≥5 tool calls OR context usage reported >20% | Both |
| `human-in-the-loop` | Action involves file deletion, external API write, financial data, profile modification, guardrail change, or irreversible operation | Both |
| `execution-provenance` | Task involves multi-agent coordination, financial data, or user explicitly requests audit trail | Both |
| `failure-mode-detection` | After ANY tool returns error, or after 2 consecutive failed attempts of same operation | Both |
| `control-plane-management` | BEFORE ANY orchestration decision involving task state, worker coordination, or board changes | Orchestrator |
| `task-decomposition` | When decomposing high-level goals into atomic Kanban tasks | Orchestrator |

### Meta Skills (Governance)

These skills govern how other skills interact or manage lifecycle. They do not independently trigger on user intent; they resolve precedence, memory rules, rollback, model routing, and multi-skill composition.

| Skill | Role |
|-------|------|
| `skill-orchestration` | Determine precedence and termination when multiple mandatory skills fire |
| `memory-lifecycle` | Govern what is read/written/deleted from persistent memory |
| `rollback-revert` | Define safe undo flow for destructive or high-stakes actions |
| `model-routing` | Select, validate, and switch models/providers with evidence |

### New Skill Integration Procedure

When adding a new skill to the stack:

1. Create the skill file under `skills/<name>/SKILL.md`.
2. If the skill is worker-relevant, add it to the worker template resource list where appropriate.
3. If the skill is orchestrator-relevant, add it to the orchestrator template resource list.
4. If the skill resolves a precedence or composition issue, reference it from `skill-orchestration`.
5. Do **not** hardcode skill names in template body outside placeholders unless they are mandatory meta-skills.
6. Update this inventory table to include the new skill.

### Orchestrator Template: Worker Profile Sync

The orchestrator template includes a worker profile sync block. Ensure this block is preserved during template updates:

- Resolve `{{AVAILABLE_WORKER_PROFILES}}` from the active profile registry.
- Do not hardcode worker names outside template placeholders.
- If a referenced worker is missing, stop assignment and escalate to the user.

## When to Use

- Creating, deploying, or verifying a Worker or Orchestrator profile
- Auditing existing SOUL.md for pre-action check coverage
- Restructuring templates for token efficiency
- Provisioning new specialist workers from an orchestrator base profile

## Procedure

### 1. Template Selection

- **Worker** → `templates/SOUL_TEMPLATE_V7.md`
- **Orchestrator** → `templates/SOUL_TEMPLATE_ORCHESTRATOR_V73.md`

### 2. Worker Provisioning Workflow (Preferred)

Use this when creating new specialist worker profiles from an existing orchestrator or base profile.

1. Create the profile by cloning an existing profile:
   ```
   hermes profile create --clone-from <base_profile> <worker_name>
   ```
2. **Required correction:** The cloned profile usually inherits the source profile's identity, worker list, and sometimes orchestrator-scoped guidance.
3. Rewrite the new profile's `SOUL.md` from the worker template (`templates/SOUL_TEMPLATE_V7.md`).
   - Replace all `{{PLACEHOLDER}}` values with profile-specific content.
   - Ensure the profile identity fields (`Name`, `Description`, `Role`, `Domain`, `Tone`) match the intended worker.
   - Ensure the **Worker profiles** list in Section C Resources reflects the actual active workers, not the source profile's list.
   - Keep only the worker skill triggers in `## Mandatory Pre-Action Checks` (exclude `control-plane-management` and `task-decomposition`).
4. Validate the deployed `SOUL.md`:
   - No `{{` or `}}` placeholders remain.
   - No HTML comments remain.
   - Every skill has a corresponding trigger in the template.
   - All skill `name:` values match their `SKILL.md` frontmatter exactly.
   - Section C has concrete content.
5. Update the orchestrator's `SOUL.md` to list the new worker in the **Worker profiles** resource block.

**Important:** `hermes profile create --clone-from ...` is a bootstrap step, not a final worker deployment. The SOUL.md rewrite is required.

### 3. Legacy Deployment Workflow (No Clone)

If creating a profile without cloning:

1. Read template in full.
2. Read relevant `skills/<name>/SKILL.md` files to understand coverage.
3. Replace `{{PLACEHOLDER}}` values with profile-specific content.
4. Include ALL mandatory pre-action checks in the template.
5. Deploy via `write_file` to `~/.hermes/profiles/<name>/SOUL.md` or `~/.hermes/SOUL.md`.
6. Post-deployment validation.

## Verification

1. No `{{` or `}}` placeholders remain.
2. No HTML comments remain.
3. Every skill has a corresponding trigger in the template.
4. All skill `name:` values match their `SKILL.md` frontmatter exactly.
5. Section C has concrete content.

## Pitfalls

Refer to `references/hybrid-architecture-enforcement.md` for the comprehensive enforcement guide. Key lessons from live sessions:

### 1. Skill Name Mismatch
Trigger references in templates MUST match the exact `name:` field in the target SKILL.md's YAML frontmatter. A typo = agent fails to load the protocol.

### 2. Improper Skill Format
Skills MUST be proper Hermes-format: `<name>/SKILL.md` with YAML frontmatter (`name`, `description`, `version`, `author`, `license`, `platforms`, `metadata.hermes.*`) + body with canonical section order: `## When to Use` → `## Procedure` → `## Pitfalls` → `## Verification`. Plain `.md` files without the folder + SKILL.md wrapper are NOT valid skills and will not be discovered by `skill_view`.

### 3. Artificial Category Subfolders
Do NOT create `worker/`, `shared/`, `orchestrator/` subfolders inside the skills directory. A skill is a skill — its trigger condition in the template determines when it's needed, not who "owns" it. Flat structure (`skills/<name>/SKILL.md`) is correct. The only role-differentiation lives in the template's trigger table.

### 4. Paraphrased Skill Content
When extracting protocols from an existing template into skills, extract VERBATIM from the original source. Paraphrasing from memory loses critical detail (anchor definitions, full failure modes, dispatcher coordination rules, instruction contamination guard, etc.). Always validate against the original template stored in `templates/original/`.

### 5. Incomplete Rename Propagation
When renaming the project or skill, update ALL locations:
- SKILL.md frontmatter `name:` field
- CHANGELOG title
- Reference file section headings (old step numbers → new skill names)
- Migration docs
- Git remote URL
- Any file that references the old name

Run `grep -r <old_name> .` to find all occurrences after any rename.

### 6. Description Length Violation
Aim ≤60 chars (spec example). If description exceeds 60 chars, trim aggressively. The Procedure section carries detail — description should only trigger recognition.

### 7. Memory Overflow When Recording Migration
After a successful migration, you MUST update memory with the new architecture state. Memory has a hard char limit (~2,200). Before writing, check current usage. If usage >80%, consolidate stale entries first (remove or merge overlapping ones) before adding the migration record. A single migration record (~350 chars) can push past the limit and fail silently. Pattern: remove the old monolithic-architecture memory entry, replace with a compact hybrid-migration entry, then confirm write success.

### 8. Section B Extraction Pattern
When extracting Section B (reasoning protocol) from a monolithic SOUL.md into flat skills:
1. Keep in SOUL.md: Section A (identity, scope, failure modes, tools, input triggers) + Section B header (knowledge priority only) + Section C (system context)
2. Replace Section B body (Steps 0-8 inline protocol) with `## Mandatory Pre-Action Checks` table
3. **Worker template**: 10 checks only (exclude orchestration-specific checks)
4. **Orchestrator template**: 12 checks (include orchestration-specific checks)
5. Workers MUST NOT act as orchestrator — no self-directed decomposition, no kanban interaction
6. Section B retains ONLY the knowledge priority list (4 lines) — nothing else
7. Verify: `#### Step 0` should NOT appear in SOUL.md post-migration

### 9. Over-Extract Pitfall Notes — Template Authority > Agent Inference
**Pattern:** When extracting pitfall notes from a live session, agents tend to over-generalize edge cases into universal rules ("workers may need orchestrator skills for self-directed tasks"). This happens because the agent reasons from a single observation rather than the architectural principle.

**Rule:** The template definition (SOUL_TEMPLATE_V7.md, SOUL_TEMPLATE_ORCHESTRATOR_V73.md) is the **source of truth** for profile-skill boundaries. Pitfall notes and migration recipes MUST NOT add skills to a profile that the template excludes. If an edge case seems to warrant a new skill for a worker, escalate to the user rather than silently adding it to the trigger table.

**Test:** After writing a pitfall note or migration step, ask: "Does this contradict the template?" If yes, the template wins — fix the note, not the template.

**Real example (2026-06-26):** Pitfall #8 initially said "Move orchestrator-only skills into trigger table — workers may need them for self-directed decomposition." This was wrong: workers are assigned tasks, they do not decompose or assign. The template V7.5 correctly excludes these. Pitfall #8 was reverted to enforce the boundary.

### 10. Strict Literal Compliance With Skill Rules
The user enforces deterministic execution: if a skill says X, do X exactly. When the user demanded "perbaiki SKILL.md," the correct response was to identify the inconsistency between two sections within the skill (Pitfall #8 vs. Skill Inventory table), reconcile them toward the authoritative source (template), and apply the fix — not to improvise a third interpretation.

**Rule:** When internal documentation contradicts itself, resolve toward the **most authoritative layer** (template > inventory table > pitfall notes > migration recipes > agent inference). Never side with the agent's own reasoning over the documented spec.

### 11. Passive Trigger Table — Skills Not Loading at Runtime
**Pattern:** Descriptive trigger tables (V7.5 and earlier) use passive "When X → load Y" format. Agent reads this as a reference — NOT as a mandatory imperative loop. Result: only skills with double-enforcement elsewhere in SOUL.md (e.g., `search-protocol` backed by Section A Tools, `intent-validation` backed by Section C Escalation) are consistently loaded. All others silently ignored.

**Root causes:**
1. **Execution-first bias:** Default agent behavior prioritizes direct action over gate checks.
2. **Non-detectable triggers:** Agent cannot measure context percentage, cannot self-classify "multi-turn," cannot detect "destructive" without concrete list.
3. **Passive format:** Tabel "When → Load" dibaca sebagai dokumentasi, bukan instruksi prosedural.
4. **No double-enforcement:** 8 dari 10 trigger hanya muncul sekali tanpa backing di Section A/C.

**Fix (V7.6):** Replace passive trigger table with **numbered imperative loop**:
- `## Mandatory Pre-Action Checks` — numbered sequence (1-10 worker, 1-11 orchestrator)
- Every check uses concrete, machine-detectable conditions
- Double-enforced in Section C Constraints: "After 5+ tool calls → context-hygiene", "Before second tool call → tool-use-discipline", "Every final answer → anti-hallucination gate"
- Communication Protocol includes pre-delivery gate as hard block

**Validation:** After deploying V7.6 template, verify >80% of applicable triggers fire in a test session.

### 12. PLAN.md Placement — Always in CWD/Project Root
**Pattern:** When creating a PLAN.md for review/approval, ALWAYS place it in the current working directory (project root), never in arbitrary paths like `~/queue-wiki/maru/` or other research output folders.

**Rule:**
- CWD atau root folder proyek = satu-satunya lokasi untuk PLAN.md
- Research output default (`~/queue-wiki/maru/`) hanya untuk riset, bukan untuk working artifact yang sedang di-review
- Jika cwd = `/home/degi/.local/share/skills/soul-core`, maka PLAN.md = `/home/degi/.local/share/skills/soul-core/PLAN.md`

**Real example (2026-06-29):** Agent membuat PLAN.md di `/home/degi/queue-wiki/maru/PLAN.md` padahal cwd = `/home/degi/.local/share/skills/soul-core`. User explicitly corrected.

### 13. No Execution From Draft Plan
**Pattern:** NEVER execute fixes from a PLAN.md that is still in draft status, even if the user describes the fixes in detail.

**Rule:**
- PLAN.md = staging area untuk review
- User harus explicitly approve sebelum eksekusi
- Agent boleh mendeskripsikan apa yang akan dilakukan, tapi TIDAK boleh menerapkan perubahan sampai approval datang
- "Tolong revise" atau "fix ini itu" dari user = minta agent deskripsikan rencana, BUKAN execute langsung

**Real example (2026-06-29):** Agent langsung mengeksekusi Fix #2 (failure-mode-detection Mode 5) padahal PLAN.md masih draft dan belum di-approve. User explicitly corrected.

### 14. Skill vs Template Consistency Audit
**Pattern:** When updating a flat skill that is referenced from SOUL.md template, always audit the template for consistency with the skill's content.

**Common conflicts:**
- Skill defines confidence levels (HIGH/MEDIUM/LOW) but template says "binary provenance only"
- Skill hardcodes specific tool names (camofox_*) but template uses generic (web_search/web_extract)
- Skill title says "Step N" but template positions it at different check number

**Rule:** After updating a skill referenced by templates, grep across all templates for conflicting terminology. Template Communication Protocol is the authoritative voice for output format; skills must align.

## References

- `templates/SOUL_TEMPLATE_V7.md` — Worker template
- `templates/SOUL_TEMPLATE_ORCHESTRATOR_V73.md` — Orchestrator template
- `templates/original/SOUL_TEMPLATE_V7.md` — Original Worker (preserved)
- `templates/original/SOUL_TEMPLATE_ORCHESTRATOR_V73.md` — Original Orchestrator (preserved)
- `references/SOUL_TEMPLATE_V7_REFERENCE.md` — Worker rationale & citations
- `references/SOUL_TEMPLATE_ORCHESTRATOR_V73_REFERENCE.md` — Orchestrator rationale & citations
- `references/CHANGELOG.md` — Version history
- `references/hybrid-architecture-migration.md` — V7.4→V7.5 migration metrics
- `references/hybrid-architecture-enforcement.md` — Enforcement guide for the hybrid pattern
- `references/in-place-migration-recipe.md` — Monolithic→Hybrid migration recipe (exact steps, validation checklist, memory overflow guard)
- `references/profile-clone-behavior.md` — Observed behavior when provisioning workers through `profile create --clone-from`, and why a post-clone SOUL.md rewrite is required for correct worker identity, triggers, and worker-list references