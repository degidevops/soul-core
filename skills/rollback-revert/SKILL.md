---
name: rollback-revert
description: "Use before reversal of destructive or high-stakes action. Defines safe undo workflow."
version: 1.0.0
author: Maru (soul-gen project)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    category: soul-core
    tags: [safety, rollback, revert, destructive]
    related_skills: [human-in-the-loop, execution-provenance, failure-mode-detection]
---

# Rollback / Revert — Safe Undo for High-Stakes Actions

## When to Use

- After a destructive action has been executed and the result is invalid or harmful
- After a high-stakes write, deployment, profile change, or irreversible operation fails post-execution
- Whenever the user requests undo, rollback, or reversal
- When a destructive action succeeds physically but fails logically (e.g., wrong target, bad parameters)

## Rule

- Do not invent rollback behavior. Use only documented reversal mechanisms for the action taken.
- If no reversal mechanism exists, escalate to the user with the exact state change and options instead of improvising recovery.

## Procedure

### 1. Identify the Action and Scope

- What was changed, written, deleted, or deployed
- Exact target path, profile name, board task ID, or system state
- Timeframe and parameters used

### 2. Determine Reversibility Tier

| Tier | Description | Examples |
|------|-------------|---------|
| A | Runtime state only | Revert in-memory state, cancel pending jobs, stop services |
| B | Local artifacts | Delete local files or directories; remote source preserved |
| C | Remote history | Rewrite or revert remote Git/API history |
| D | Baseline reset | Restore bundled/system baseline |

### 3. Present Options (A/B/C/D)

- State the exact extent of each tier.
- State default recommended tier.
- Ask the user to confirm one tier before acting.

### 4. Preserve Evidence

- Before rollback, capture current state: file contents, task status, provenance trace, error output.
- Store captured evidence in a backup path or session log.

### 5. Execute Verified Reversal

- Use documented commands only (`git revert`, backup restore, kanban state transition, etc.)
- Do not delete or overwrite unless explicitly confirmed.

### 6. Verify Restoration

- Confirm target returned to expected state.
- If verification fails, stop and escalate rather than forcing another write.

## Pitfalls

- Do not auto-select a higher tier than necessary.
- Do not improvise custom rollback logic for unfamiliar systems.
- Do not treat rollback as a substitute for human-in-the-loop on the original action.
- Do not proceed without explicit tier confirmation for tiers B and above.

## Verification

- Action scope is fully identified
- Reversibility tier is explicit and user-confirmed
- Pre-rollback evidence was captured
- Reversal command is documented and appropriate for the target system
- Post-rollback state matches expected baseline