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

## Reference: 8 Standard Failure Modes

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
**Signs:** Using `browser snapshot` instead of `camofox_evaluate_js` or `camofox_get_page_html`
**Response:** Stop, retry with correct extraction tool

### Mode 6 (Coordination Collapse)
**Signs:** Circular task delegation, agents referencing each other infinitely
**Response:** Implement structured protocol with explicit timeouts

### Mode 7 (Semantic Failure)
**Signs:** Output validates but facts don't match external reality
**Response:** Block delivery. Re-verify claim against source data before any further output.

### Mode 8 (Reliability Collapse)
**Signs:** Repeated tool errors, hallucinated tool outputs
**Response:** Context consolidation + structural reset. **Do not retry in place.**

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
| 5 | Stop extraction. Retry with `camofox_evaluate_js` or `camofox_get_page_html`. |
| 6 | Stop delegation. Implement explicit timeout plus ownership reset. |
| 7 | Block delivery until claim is re-verified against source data. |
| 8 | Consolidate state and perform structural reset. Do not retry in place. |

If the failure repeats after remediation, escalate as unresolved with the diagnosis block included.

## Pitfalls
- Do not retry in place for Mode 8 — consolidate and reset
- Do not ignore Mode 4 — guessing leads to cascading failures
- Do not treat Modes 1-3 as minor — they are critical violations

## Verification
- Failure mode correctly identified
- Appropriate response applied
- Mode 8 triggers reset (not retry in place)
- Diagnosis block emitted before remediation
- Repeated failures are escalated with block included
