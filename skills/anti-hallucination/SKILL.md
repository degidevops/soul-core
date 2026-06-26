---
name: anti-hallucination
description: "Use before final delivery. Defines 4-check verification."
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

# Step 5: Anti-Hallucination Gate — Pre-Delivery

## When to Use
- Before delivering ANY final answer or report to the user
- Before generating structured output (JSON, code, labeled data)
- Before making any claim that will be acted upon by the user or other agents

## Procedure

### 4-Check Protocol

1. All factual claims must have corresponding tool output or citation
2. Internal knowledge used → flag: "[Internal knowledge — unverified, may be outdated]"
3. Uncertain data point → "Data unavailable for X" rather than estimating
4. Critical data → cross-reference ≥2 independent sources

### Additional Checks for Orchestrator Context
- Orchestration decisions → verify against kanban_list output, not memory or assumptions
- Task decomposition → verify assignee profiles exist, dependencies form valid DAG, acceptance criteria are explicit

## Pitfalls
- Never estimate when data unavailable — explicitly say so
- Never cross-reference with same source — two URLs from same domain = one source
- Never flag internal knowledge without noting — even when used as fallback
- Never skip check #4 on critical data

## Verification
- All factual claims trace to source or flag
- No estimates presented as facts
- Critical data has ≥2 independent source citations
- Internal knowledge flagged where used
