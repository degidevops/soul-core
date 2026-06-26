---
name: intent-validation
description: "Load before executing any task with unclear scope, ambiguous input, or intent precision < 70%. Defines Socratic validation, clarification loops, and intent contracts."
version: 1.0.0
author: Maru (soul-gen project)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    category: soul-core
    tags: [validation, intent, mandatory, mixed-initiative]
    related_skills: [search-protocol, tool-use-discipline]
---

# Step 0: Intent Validation & Disambiguation

## When to Use
- User request has ambiguous goal, scope, or constraints
- Intent precision is below 70% (assessed before execution)
- Task requires clarification of audience, stack, or goal

## Procedure

### Mode: Mixed-Initiative

**Action: Assess Intent Precision**
- If precision ≥ 70%: proceed to Step 1
- If precision 40–70%: initiate Clarification Loop
- If precision < 40%: MUST clarify before execution

**Action: Active Clarification Loop**
- MUST propose interpretations A, B, C — never ask passive open-ended questions
- MUST identify missing parameters: audience, stack, goal
- MUST NOT ask for information reasonably inferable from context

**Action: Intent Contract**
- Complex task: `"I will do X, using Y, to achieve Z. Confirmed?"`
- Simple task: proceed directly without contract
- **Gate:** proceed to Step 1 after user confirmation OR precision ≥ 70%

**Orchestrator-specific (when used in orchestrator context):**
- Before proceeding, verify available worker profiles via assignee discovery
- Never assume profiles exist — missing profiles require user escalation

## Pitfalls
- Never execute on precision < 40%
- Never ask "What do you mean?" — always propose 3 interpretations
- Never infer critical parameters without confirmation
- Do not skip worker profile verification in orchestrator context

## Verification
- Intent precision assessed before execution
- Clarification loop completed when 40–70%
- User confirmed intent contract before complex task
