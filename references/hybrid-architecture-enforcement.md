# Hybrid Architecture Enforcement

## Pattern Summary

Extract procedural protocols from monolithic SOUL.md templates into standalone skills. SOUL.md retains identity + mandatory triggers; protocol detail lives in `skills/<name>/SKILL.md`.

## Structural Rules

### SOUL.md (always-loaded layer)
- Identity, scope, failure modes, tools, hard guardrails, confidence framework, communication protocol
- `## Mandatory Skill Triggers` section with `skill_view(name="...")` calls

### Skills (on-demand layer)
- One folder per protocol: `skills/<name>/SKILL.md`
- Hermes canonical format: YAML frontmatter + `## When to Use` → `## Procedure` → `## Pitfalls` → `## Verification`
- Flat structure — NO category subfolders (worker/shared/orchestrator)
- Description ≤60 chars (spec example), ≤1024 chars (validator limit)

### Trigger Format in Template
```markdown
| When | Skill to Load |
|------|---------------|
| Before X | `skill_view(name="skill-name")` |
```

## Common Pitfalls

### 1. Skill Name Mismatch
Trigger references MUST match exact `name:` field in SKILL.md frontmatter.

### 2. Improper Skill Format
Plain `.md` files without `skills/<name>/SKILL.md` wrapper are NOT valid — `skill_view` won't discover them.

### 3. Artificial Category Subfolders
Do NOT create worker/shared/orchestrator subfolders. Trigger condition determines usage, not folder ownership.

### 4. Paraphrased Skill Content
Extract VERBATIM from original source. Paraphrasing from memory loses critical detail. Always validate against `templates/original/`.

### 5. Incomplete Rename Propagation
When renaming project:
- SKILL.md frontmatter `name:` field
- CHANGELOG title
- Reference file section headings (Step N → Skill Name)
- Migration docs
- Git remote URL

### 6. Description Length Violation
Aim ≤60 chars. Trim aggressively. If still the description tries to do too much — split into description + Procedure section.

## Content Extraction Checklist

When extracting a protocol from template to skill:
1. Copy full Procedure content verbatim (including sub-sections like Semantic Grounding, Evidence Ranking, etc.)
2. Include ALL security/safety rules (Instruction Contamination Guard, PROCEED_SIGNAL restriction)
3. Include ALL decision tables (failure modes, column awareness, tool restrictions)
4. Include ALL output formats (JSONL trace, DAG tree, tuple concretization rules)
5. Preserve keywords that appear in other skills' cross-references
6. Validate: diff original template vs (new template + combined skill files) — no keyword loss

## Validation Method

1. Extract all keywords from each section of original template
2. Search each keyword in (new template + combined skill files)
3. Any missing keyword = content loss = must fix
4. Original templates MUST be preserved in `templates/original/`

## Reference Trail

- Source: `templates/original/SOUL_TEMPLATE_V7.md` lines 131-327 (Worker Section B: Steps 0-8)
- Source: `templates/original/SOUL_TEMPLATE_ORCHESTRATOR_V73.md` lines 221-535 (Orchestrator Section B: Steps 0-10)
- Migration date: 2026-06-26
- Template versions: V7.4 → V7.5
