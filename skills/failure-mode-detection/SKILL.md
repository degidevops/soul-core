---
name: failure-mode-detection
description: "Use when anomalies detected. Defines 8 failure modes."
version: 1.0.0
author: Maru (soul-gen project)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    category: soul-core
    tags: [failure, detection, diagnosis, reference]
    related_skills: [context-hygiene, tool-use-discipline]
---

# Failure Mode Detection & Response

## When to Use
- Agent detects anomalies in its own behavior
- Repeated tool errors or stalled progress
- User reports agent misbehavior
- As reference during post-mortem analysis

## Reference: 9 Standard Failure Modes

| # | Title | Behavior | Retry | Severity |
|---|-------|----------|-------|----------|
| 1 | Answering Without Searching | factual claim without web_search | no_retry | critical |
| 2 | Internal Knowledge as Factual Source | presenting parametric knowledge as verified fact | no_retry | critical |
| 3 | Silent Parametric Fallback | substituting internal knowledge when search fails | no_retry | critical |
| 4 | Silent Intent Guessing | executing on assumptions when input is ambiguous | clarification_loop | high |
| 5 | Snapshot-Based Extraction | using browser snapshots for content extraction | use_camofox_eval_or_html | high |
| 6 | Multi-Agent Coordination Collapse | misinterpreted inter-agent messages, groupthink, circular deps, cascading injection | structured_protocol_with_timeouts | high |
| 7 | Semantic Failure | structured output passes schema but is factually incorrect | external_verifier_required | critical |
| 8 | Tool-Call Reliability Collapse | failed tool execution as context grows across turns | context_consolidation_then_reset | high |
| 9 | Skill Load Failure | mandatory skill_view returns empty, error, or no content — agent attempts to improvise the protocol from memory | hard_stop_and_report | critical |

### Orchestrator-Specific Failure Modes

| # | Title | Behavior | Retry | Severity |
|---|-------|----------|-------|----------|
| O8 | Orchestrator Executing Worker Tasks | performs implementation instead of delegating | immediate_halt_and_reassign | critical |
| O9 | Ignoring Worker Block Signals | fails to respond to kanban_block from workers | check_board_and_intervene | high |
| O10 | Context Pollution into Workers | orchestrator context bleeds into worker sessions | reinject_kanban_guidance | high |
| O11 | Over/Under Decomposition | tasks too small or too large | redecompose_with_granularity_check | medium |
| O12 | Silent Worker Failure | worker dies silently, task stuck claimed | periodic_liveness_audit | medium |

## Detection Guidelines

### Modes 1-3 (Parametric Hallucination)
**Signs:** Claims without citations, "I know that..." patterns, answers without tool calls
**Response:** STOP, flag as violation, require search

### Mode 4 (Intent Guessing)
**Signs:** Agent proceeds without asking despite unclear instruction
**Response:** Initiate clarification loop immediately

### Mode 5 (Snapshot Extraction)
**Signs:** Using `browser snapshot` instead of the extraction hierarchy defined in search-protocol
**Response:** Stop, retry with correct extraction tool per the hierarchy:
1. `web_search` — primary retrieval (SearXNG or equivalent)
2. `web_extract` — content extraction (Prebextor or equivalent)
3. `camofox_evaluate_js` — surgical JS extraction (SearXNG backend only)
4. `camofox_get_page_html` — full rendered HTML (SearXNG backend only, last resort)
- NEVER: browser snapshots for content extraction

### Mode 6 (Coordination Collapse)
**Signs:** Circular task delegation, agents referencing each other infinitely
**Response:** Implement structured protocol with explicit timeouts

### Mode 7 (Semantic Failure)
**Signs:** Output validates but facts don't match external reality
**Response:** Block delivery. Re-verify claim against source data before any further output.

### Mode 8 (Reliability Collapse)
**Signs:** Repeated tool errors, hallucinated tool outputs
**Response:** Context consolidation + structural reset. **Do not retry in place.**

### Mode 9 (Skill Load Failure — Improvised Protocol)
**Signs:** `skill_view(name="...")` returns empty/error, but agent proceeds to "guess" the protocol steps from internal knowledge. Agent says "I will follow the standard procedure..." without having loaded the skill.
**Response:** HARD STOP. Do NOT improvise. Do NOT guess the protocol. Emit the standard failure report:
```
[CRITICAL] Mandatory skill {{SKILL_NAME}} failed to load.
Deterministic execution is impossible.
All operations halted to prevent improvised protocol failure.
Root cause: {{ERROR_MESSAGE}}
Action required: Verify skill exists at skills/{{SKILL_NAME}}/SKILL.md and retry.
```
**Retry allowed:** NO — user must fix the underlying skill availability first.

### Compact Diagnosis Output

Before applying remediation, emit a short diagnosis block:

```
failure_mode: <mode_id>
trigger: <observed symptom>
root_cause: <assessed cause>
action: <remediation to apply>
retry_allowed: <yes|no>
```

Required actions by mode:

| Mode | Required Action |
|------|-----------------|
| 1 | Stop. Require search before any further factual statement. |
| 2 | Stop. Flag as violation. Convert claim to unverified pending search. |
| 3 | Stop. Do not silently substitute internal knowledge. Surface required search. |
| 4 | Initiate clarification loop immediately. Do not proceed on assumption. |
| 5 | Stop extraction. Retry with the extraction hierarchy: `web_search` → `web_extract` → (if SearXNG backend: `camofox_evaluate_js` → `camofox_get_page_html`). NEVER browser snapshots. |
| 6 | Stop delegation. Implement explicit timeout plus ownership reset. |
| 7 | Block delivery until claim is re-verified against source data. |
| 8 | Consolidate state and perform structural reset. Do not retry in place. |
| 9 | HARD STOP. Do not improvise. Report missing skill and halt all operations until user resolves. |

If the failure repeats after remediation, escalate as unresolved with the diagnosis block included.

## Pitfalls
- Do not retry in place for Mode 8 — consolidate and reset
- Do not ignore Mode 4 — guessing leads to cascading failures
- Do not treat Modes 1-3 as minor — they are critical violations
- **Mode 9 is a hard stop:** NEVER improvise a protocol from memory when skill_view fails. An improvised protocol is indistinguishable from a hallucination — both produce confident but ungrounded execution.

## Verification
- Failure mode correctly identified
- Appropriate response applied
- Mode 8 triggers reset (not retry in place)
- Diagnosis block emitted before remediation
- Repeated failures are escalated with block included
