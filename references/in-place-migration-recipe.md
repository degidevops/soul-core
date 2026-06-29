# In-Place SOUL.md Migration Recipe (Monolithic → Hybrid)

## Source Session
2026-06-26 — Migrated Maru's default profile SOUL.md from monolithic (Section B inline, ~120 lines) to hybrid (triggers table only, protocols in flat skills).

## When to Use
- SOUL.md has grown beyond ~15KB with inline protocol detail
- Context window pressure from always-loaded protocol text
- Agent is skipping protocol steps because they're buried in Section B
- You want to add a new protocol without bloating SOUL.md

## Pre-Migration Checklist
1. Verify all flat skills exist and are loadable:
   - **Worker**: 10 skills (`intent-validation`, `search-protocol`, `conflict-resolution`, `tool-use-discipline`, `reasoning-integrity`, `anti-hallucination`, `context-hygiene`, `human-in-the-loop`, `execution-provenance`, `failure-mode-detection`)
   - **Orchestrator**: 12 skills (above + `task-decomposition`, `control-plane-management`)
2. Back up current SOUL.md (read it fully — you'll need to preserve Section A and Section C verbatim)
3. Check memory usage before planning the post-migration memory update

## Migration Steps (exact order from 2026-06-26)

### Step 1: Write new SOUL.md
Use `write_file` (NOT patch) — full replacement is safer than patching when removing large blocks.

**Worker structure (10 skill triggers):**
```
# SOUL Configuration — <Name>

## Section A          ← copy verbatim from current SOUL.md
### Identity
### Scope
### Failure Modes
### Input triggers
### Tools

---                     ← horizontal rule

## Mandatory Skill Triggers   ← NEW: replaces Section B body
| When | Skill to Load |
|------|---------------|
| Before ANY non-trivial task with unclear scope/goal | `skill_view(name="intent-validation")` |
| Before making ANY factual claim based on external data | `skill_view(name="search-protocol")` |
| When external sources contradict each other or internal knowledge | `skill_view(name="conflict-resolution")` |
| Before ANY tool call in multi-turn or multi-step context | `skill_view(name="tool-use-discipline")` |
| When conclusions require multi-step reasoning or analysis | `skill_view(name="reasoning-integrity")` |
|| `skill_view(name="context-hygiene")` |
|| When context usage exceeds >50,000 tokens or after compression | `skill_view(name="context-hygiene")` |
| Before ANY destructive, irreversible, or high-stakes action | `skill_view(name="human-in-the-loop")` |
| When emitting structured trace events is required | `skill_view(name="execution-provenance")` |
| When detecting or responding to operational failures | `skill_view(name="failure-mode-detection")` |

**Critical Rule:** If a skill trigger applies, load the skill FIRST, follow its protocol, THEN act. Do not improvise versions of these protocols from memory.

---

## Section B — Knowledge Priority   ← KEPT: only the 4-line priority list
1. Live internet — web_search + web_extract + js + html — ONLY valid factual source
2. User-provided files — file_read — project-specific context
3. Persistent memory — user preferences ONLY, never facts
4. Internal parametric knowledge — DISABLED for factual use

---

## Confidence Framework   ← KEPT: VERIFIED/PARTIAL/UNVERIFIED/CONFLICTING definitions

---

## Communication Protocol   ← KEPT: output format + grounding rules

---

## Section C   ← copy verbatim from current SOUL.md
### System Context
```

**Orchestrator structure (12 skill triggers):** Same as worker but add these 2 rows:
```
| When decomposing goals into atomic tasks | `skill_view(name="task-decomposition")` |
| When interacting with Kanban board or making orchestration decisions | `skill_view(name="control-plane-management")` |
```

### Step 2: Post-write validation (execute_code or manual check)
- `{{` not in content
- `<!--` not in content
- `## Mandatory Skill Triggers` in content
- `#### Step 0` NOT in content (confirms Section B extraction succeeded)
- Identity keywords preserved (agent name, role)
- Failure modes preserved (Mode 1 through Mode 8)
- Tools preserved (web_search, bypass signal)
- Hard guardrails preserved

### Step 3: Load all skills to confirm accessibility

**Worker** (10 skills):
```python
for skill in ["intent-validation", "search-protocol", "conflict-resolution",
              "tool-use-discipline", "reasoning-integrity", "anti-hallucination",
              "context-hygiene", "human-in-the-loop", "execution-provenance",
              "failure-mode-detection"]:
    skill_view(name=skill)  # must return success=True
```

**Orchestrator** (12 skills — includes the 2 above):
```python
for skill in ["intent-validation", "search-protocol", "conflict-resolution",
              "tool-use-discipline", "reasoning-integrity", "anti-hallucination",
              "context-hygiene", "human-in-the-loop", "execution-provenance",
              "failure-mode-detection", "task-decomposition", "control-plane-management"]:
    skill_view(name=skill)  # must return success=True
```

### Step 4: Update memory (CAUTION: check char limit first)
Memory limit is ~2,200 chars. A migration record is ~300-400 chars. If usage >80%, remove a stale entry first, then add:
- Remove: old monolithic-architecture memory entry
- Add: compact hybrid-migration entry

## Key Lessons from 2026-06-26
1. `execute_code` cannot import `skill_view` from `hermes_tools` — use the tool directly, not programmatically
2. Skills live in `~/.local/share/skills/soul-core/skills/<name>/SKILL.md`, NOT in `~/.hermes/skills/`
3. `skills_list(category="soul-core")` confirms registry; `skill_view(name="...")` confirms accessibility
4. The `patch` tool works well for adding the 2 orchestrator skills to the trigger table after initial write
5. Memory overflow: the first `memory(action="add")` failed silently at 2,128/2,200 chars — always check usage before writing
6. **Template authority**: When template and pitfall notes conflict, the template wins. Never add orchestrator-only skills to worker profiles based on edge-case reasoning.
