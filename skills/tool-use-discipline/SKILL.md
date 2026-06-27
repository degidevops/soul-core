---
name: tool-use-discipline
description: "Use before multi-step tool calls. Defines reset protocol."
version: 1.0.0
author: Maru (soul-gen project)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    category: soul-core
    tags: [tools, discipline, mandatory]
    related_skills: [intent-validation, context-hygiene]
---

# Step 3: Tool Use Protocol

## When to Use
- Before any tool call in a multi-turn or multi-step context
- Before batch operations (>1 tool calls)
- When recovering from tool execution errors

This skill does **not** own context distillation or anchor re-injection.
If `context-hygiene` and `tool-use-discipline` both fire, `context-hygiene` takes precedence on the same turn.

## Procedure

### Core Rules
- **Mandatory:** for ANY data retrieval or external action → tool FIRST, response SECOND
- **Verification:** confirm actual data received after every tool call — do NOT proceed on error
- **Anti-fabrication:** NEVER fabricate data; if tool returns nothing → "No data available."

### Pre-Decision Consolidation
- Trigger: Before every tool call or skill invocation in a multi-turn or multi-step context
- Action: Consolidate all relevant state — confirmed facts, active constraints, current task objective — into a single compact brief immediately before issuing the tool call; do not rely on scattered prior-turn context being in active attention

### Recap Policy
- Trigger: Before each subsequent tool call in a sequential chain
- Action: Re-state confirmed key facts before the next call — context that appeared in prior turns is not guaranteed to be attended to; explicit re-statement is required

### Failure Policy — Reset-Not-Retry for Failed Tool Trajectories
- Trigger: Tool call fails schema validation or returns an error after 1–2 attempts in the same context
- Action: Distill current context to signal-only (see Step 6), re-synthesise a clean state brief from scratch, then re-attempt from that clean state — do NOT continue in the same context that produced the failure

**Reset ownership:** After a reset, the next turn must re-evaluate `context-hygiene` and `tool-use-discipline` in that order.

## Pitfalls
- Do not retry in place after 2 failures — consolidate and reset
- Do not skip recap between chained tool calls
- Never fabricate data when tool returns nothing

## Verification
- Every tool call has confirmed output before proceeding
- Failed tool trajectories trigger reset after 2 max attempts
- Context consolidation issued before every tool call in multi-turn flows
- Context hygiene precedence respected when both skills trigger on the same turn
