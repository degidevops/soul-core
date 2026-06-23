# SOUL Deployment Pitfalls — 2026-06-23

Session: Degi requested SOUL.md update via soul-management skill. Multiple failed attempts revealed systemic issues.

## Issues Encountered

### 1. `sed` destroys multi-line XML content
**Symptom:** `sed -i 's|<position></position>|...|'` produced empty `<position></position>` in output.
**Root cause:** `sed` is line-oriented. Multi-line content with `&amp;`, `&lt;`, `"` and newlines breaks `sed` substitution.
**Fix:** Always use `write_file` for the entire file content. Never `sed`/`awk` for XML.

### 2. Comment handling was inconsistent
**Symptom:** First attempt removed all HTML comments (including section markers). Second attempt kept some.
**Root cause:** Template instruction says "Keep section comments intact" but skill says "Remove all HTML comments" — contradictory.
**Fix:** Skill now has explicit "Comment Taxonomy" table distinguishing instruction header (remove), section dividers (keep), inline annotations (remove).

### 3. Section B not closed, Section C missing opening
**Symptom:** Deployed SOUL.md had unclosed `<section_b>` and missing `<section_c>` opening tag.
**Root cause:** Incremental `patch` operations on existing file instead of writing from scratch.
**Fix:** Write entire file via `write_file`. Verify tag balance after deployment.

### 4. Empty Section C fields
**Symptom:** `<position></position>` and `<escalation></escalation>` were empty after deployment.
**Root cause:** `sed` substitution failed silently for multi-line content. Agent did not verify before reporting success.
**Fix:** Extract values from existing SOUL.md BEFORE deploying. Verify every field has content.

## Verification Checklist
After every SOUL deployment:
1. `grep -c '<section_'` == `grep -c '</section_'` (tag balance)
2. No `{{` remaining (all placeholders filled)
3. No `<!--` inside sections (inline annotations removed)
4. No `<metadata>` block
5. Root tag is `<soul_config>` not `<soul_template>`
6. Every Section C field has non-empty content
