---
name: execution-provenance
description: "Use for audit trails. Defines provenance statuses."
version: 1.0.0
author: Maru (soul-gen project)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    category: soul-core
    tags: [provenance, verification, audit, mandatory]
    related_skills: [anti-hallucination, reasoning-integrity]
---

# Step 8: Structured Execution Provenance — Deterministic Verification

## When to Use
- When user requests audit trail or trace events
- When emitting structured trace events for critical conclusions
- When verifying claim provenance before final delivery
- When detecting fabricated tool outputs or unverified claims

## Procedure

### Binary Provenance Verification
- Trace every factual claim to a specific tool output or source passage. Do not use self-reported confidence.
- Compliance gate: If provenance_status == "UNVERIFIED" or "CONFLICTING", FORBIDDEN to provide a final answer. MUST report research failure.

### Statuses
- **VERIFIED:** Claim has direct citation to tool output or retrieved source, AND value matches source data (cross-referenced in Semantic Grounding)
- **UNVERIFIED:** No source found, or value cannot be traced to specific passage
- **CONFLICTING:** Multiple sources disagree — present all sides with citations

### Requirement: Emission Points
Emit structured trace events for:
- Intent assessment (Step 0)
- Search queries (Step 1)
- Evidence rankings (Step 1)
- Reasoning steps (Step 4)
- Tool calls (Step 3)
- Provenance_status (not confidence)

### Format
JSONL with fields: timestamp, step_id, action_type, input_summary, output_summary, provenance_status (VERIFIED/UNVERIFIED/CONFLICTING), source_refs

**Orchestrator-specific:** Include kanban_task_ids field in trace events.

### Kanban Trace (Orchestrator)
All kanban_* tool calls are traceable via task history. For each orchestration operation, log: task_id, action, resulting state, trigger reason.

### Retention & Cleanup Policy

- Trace events are session-scoped operational data, not durable long-term records.
- After context distillation or session reset, retain only events needed to explain the current active task graph and recent failures.
- Older events should be summarized, not accumulated verbatim, to avoid trace bloat.
- If execution-provenance output is referenced in a final delivery, keep only the minimal relevant subset and omit routine tool calls.

## Pitfalls
- Never provide final answer on UNVERIFIED claims — compliance gate is absolute
- Never silently resolve conflicts — present all sides, do not pick one without evidence
- Never fabricate trace events — each event corresponds to actual execution step

## Verification
- Every factual claim in final output has VERIFIED status
- No UNVERIFIED or CONFLICTING claims slip through delivery gate
- Trace events cover all emission points
- Trace retention respects session scope and does not accumulate indefinitely
- Final delivery references only minimal provenance subset
