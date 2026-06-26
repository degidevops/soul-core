---
name: human-in-the-loop
description: "Use before destructive actions. Defines A/B/C confirmation."
version: 1.0.0
author: Maru (soul-gen project)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    category: soul-core
    tags: [shared, safety, hitl, destructive-ops, mandatory]
    related_skills: [anti-hallucination, context-hygiene]
---

# Step 7: Human-in-the-Loop Verification Gate

## When to Use
- File deletion or major file restructuring
- External API writes (posts, messages, publications)
- Financial transactions or trading operations
- Security policy violations or bypass attempts
- Changing agent identity, behavioral rules, or hard guardrails
- Overriding another agent's confirmed output

**Orchestrator-specific triggers:**
- New profile creation, board restructure, destructive ops
- Task reassignment to different profile category
- Archiving completed chains
- Overriding worker blocks

## Procedure

### 3-Step Confirmation

1. **Explicit confirmation request** with:
   - A/B/C options (concrete, mutually exclusive)
   - Risk summary for each option
   - Recommended option with rationale
   - **Orchestrator:** include current kanban_list snapshot

2. **Timeout with safe default:**
   - Default = ABORT (never proceed on silence)
   - Timeout duration: reasonable for context (don't rush)

3. **Audit log entry:**
   - What was decided
   - Why (trigger + reasoning)
   - What was the alternative
   - Timestamp

### PROCEED_SIGNAL Restriction
The `{{PROCEED_SIGNAL}}` bypass ONLY skips Step 1 (search protocol) — it does NOT bypass this gate.

## Pitfalls
- Never auto-proceed on timeout — safe default is ALWAYS abort
- Never use PROCEED_SIGNAL for high-stakes operations — it only bypasses search
- Never skip confirmation for destructive operations even if user seems impatient
- Never phrase confirmation passively — use concrete A/B/C options

## Verification
- High-stakes action detected
- Confirmation presented with A/B/C + risk summary
- User explicitly confirmed before action
- Audit log entry complete
