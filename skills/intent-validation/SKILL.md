---
name: intent-validation
description: "Use when task scope is unclear. Defines clarification loops."
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

# Intent Validation & Disambiguation — Mixed-Initiative Protocol

## When to Use
- User request has ambiguous goal, scope, or constraints
- Intent clarity is LOW (assessed before execution)
- Task requires clarification of audience, stack, or goal

## Procedure

### Mode: Mixed-Initiative

**Action: Assess Intent Clarity**
- **HIGH clarity (proceed directly):** User provides ≥1 action verb + ≥1 target object + domain context is clear
- **MEDIUM clarity (clarify):** Missing one of: no action verb, no target object, or ambiguous domain
- **LOW clarity (MUST clarify):** <3 words total, or topic drop without context, or multiple conflicting intents in one message

**Action: Active Clarification Loop**
- MUST propose interpretations A, B, C — never ask passive open-ended questions
- MUST identify missing parameters: audience, stack, goal
- MUST NOT ask for information reasonably inferable from context

**Action: Intent Contract**
- Complex task: `"I will do X, using Y, to achieve Z. Confirmed?"`
- Simple task: proceed directly without contract
- **Gate:** proceed after user confirmation OR intent clarity is HIGH

**Orchestrator-specific (when used in orchestrator context):**
- Before proceeding, verify available worker profiles via assignee discovery
- Never assume profiles exist — missing profiles require user escalation

## Pitfalls
- Never execute on LOW clarity — always propose 3 interpretations
- Never ask "What do you mean?" — always propose 3 interpretations
- Never infer critical parameters without confirmation
- Do not skip worker profile verification in orchestrator context
- **Topic-drop context bleed:** When user explicitly drops a topic ("lupakan", "forget", "never mind", "gajadi"), do not carry over references, context, or framing from that topic into subsequent responses. Treat as a clean slate for the new intent.

## Verification
- Intent clarity assessed before execution using concrete criteria (action verb + target object + domain context)
- Clarification loop completed when MEDIUM or LOW clarity detected
- User confirmed intent contract before complex task
