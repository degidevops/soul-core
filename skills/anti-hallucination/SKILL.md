---
name: anti-hallucination
description: "Use before final delivery. Defines 5-check verification including protocol provenance."
version: 1.0.0
author: Maru (soul-gen project)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    category: soul-core
    tags: [shared, verification, hallucination, pre-delivery, mandatory]
    related_skills: [search-protocol, execution-provenance]
---

# Anti-Hallucination Gate — Pre-Delivery Verification

## When to Use
- Before delivering ANY final answer or report to the user
- Before generating structured output (JSON, code, labeled data)
- Before making any claim that will be acted upon by the user or other agents

## Procedure

### 5-Check Protocol

1. All factual claims must have corresponding tool output or citation
2. Internal knowledge used → flag: "[Internal knowledge — unverified, may be outdated]"
3. Uncertain data point → "Data unavailable for X" rather than estimating
4. Critical data → cross-reference ≥2 independent sources
5. **Protocol Provenance:** All procedural steps taken in this response MUST trace to a successfully loaded `skill_view` output within this session. If a mandatory skill failed to load and the protocol was executed anyway → **GATE FAILURE**. Halt delivery and report Mode 9 (Skill Load Failure).

### Provenance Awareness

- Before applying check #4 for critical data, inspect whether `execution-provenance` has emitted a trace or status for the current claim in this session.
- If provenance status is `CONFLICTING`, do not deliver until the conflict is resolved or presented as unresolved with sources.
- If no provenance is available for a critical claim, downgrade the claim to `UNVERIFIED` instead of forcing a second-source search.

### Output Schema Validation

- Before final delivery, validate deliverable shape against the contract implied by the current task:
  - Code: ensure syntax validity and that referenced APIs/files exist in retrieved context
  - JSON/YAML: check well-formedness and that values correspond to verified claims
  - Tables/lists: verify row/column counts align with retrieved data
  - Command sequences: inspect for shell-injection patterns and unintended side effects
- If schema is invalid or contains unverified values, halt delivery and return to source rather than patching in place.

### Additional Checks for Orchestrator Context
- Orchestration decisions → verify against kanban_list output, not memory or assumptions
- Task decomposition → verify assignee profiles exist, dependencies form valid DAG, acceptance criteria are explicit

## Pitfalls
- Never estimate when data unavailable — explicitly say so
- Never cross-reference with same source — two URLs from same domain = one source
- Never flag internal knowledge without noting — even when used as fallback
- Never skip check #4 on critical data
- **Never deliver a response that relies on a protocol from memory** when `skill_view` failed. Check #5 is a hard gate — improvised protocols are hallucinations wearing a procedure's clothing.

## Verification
- All factual claims trace to source or flag
- No estimates presented as facts
- Critical data has ≥2 independent source citations
- Internal knowledge flagged where used
- Provenance status reviewed before delivery when trace is present
- Conflicting provenance blocks delivery until resolved
- Output schema validated before delivery
- Schema violations halt delivery and return to source
- **Protocol provenance verified:** All mandatory skill_view calls in this session returned valid content before any protocol step was executed
