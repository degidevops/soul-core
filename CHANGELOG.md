# Changelog — soul-management

## [Unreleased] — 2026-06-23

### Fixed
- **Placeholder format compliance** — Added mandatory format check in both Worker (V7) and Orchestrator (V73) deployment workflows. Agent must now follow the exact format specified in each placeholder's GUIDANCE/Example. Previously, agents would fill placeholders with free-form text ignoring the specified format (e.g., `{{SYSTEM_POSITION}}` requires `"[Role] for [context] inside [system]; [function] only; [key constraint]."` but agents would write multi-sentence descriptions).
- **Post-deployment validation** — Added mandatory XML tag balance check (`grep -c '<section_'` vs `grep -c '</section_'`), placeholder leak check (no `{{` remaining), and HTML comment leak check. If any check fails, agent must fix immediately and NOT report success.
- **Simplified comment handling** — Removed "Preserve section divider comments" instructions from both Worker and Orchestrator workflows. All HTML comments (`<!-- ... -->`) are now removed on deployment without exception. XML-style tags (`<section_a>`, `<step n="0">`, etc.) already provide structural navigation — comment markers are redundant noise.

### Root Cause Analysis
- **Problem 1:** Agent (Maru) filled `{{SYSTEM_POSITION}}` with free-form text `"Maru — default profile. Host: WSL (Windows Subsystem for Linux). User: Degi."` instead of the mandatory format `"[Role] for [context] inside [system]; [function] only; [key constraint]."`
- **Cause 1:** No enforcement step in the deployment workflow. The template had guidance + example, and SKILL.md had format strings, but nothing in the workflow *required* the agent to verify format compliance before deploying.
- **Fix 1:** Added explicit "Placeholder compliance check" step to both workflows.

- **Problem 2:** Conflicting instructions about comment handling. Template instruction 9 said "Keep section comments intact" but SKILL.md said "Remove all HTML comments". Agent was confused about which comments to keep.
- **Cause 2:** Ambiguous taxonomy — "section dividers" vs "inline annotations" vs "instruction header" created false distinctions. In practice, all comments are noise in the deployed profile.
- **Fix 2:** Simplified to "Remove all HTML comments". XML tags provide navigation. Updated Comment Taxonomy → Comment Handling with clear "remove everything" rule.

### Changed
- Worker deployment workflow: 8 → 9 steps (removed "Preserve section dividers", added compliance check + validation)
- Orchestrator deployment workflow: 8 → 9 steps (removed "Preserve section dividers", added compliance check + validation)
- Comment Taxonomy section → simplified to "Comment Handling" (remove all, no exceptions)
- Pitfalls point 2 updated to match new comment handling rule
