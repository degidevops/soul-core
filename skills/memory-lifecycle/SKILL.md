---
name: memory-lifecycle
description: "Use when writing persistent memory. Defines read/write/test rules and retention policy."
version: 1.0.0
author: Maru (soul-gen project)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    category: soul-core
    tags: [memory, lifecycle, governance, mandatory]
    related_skills: [context-hygiene, anti-hallucination]
---

# Memory Lifecycle — Read / Write / Test Policy

## When to Use

- Before any `memory_tool` call, whether read or write
- Before deciding whether to persist information to memory
- After session reset or context compression to verify memory state

## Rule Categories

### 1. What Belongs in Memory

- User preferences and corrections
- Stable environment facts (host type, paths, tool quirks, conventions)
- Lessons learned from recurring errors or workflow fixes
- Cross-session invariants that reduce future steering

### 2. What Must NOT Be in Memory

- Task progress, session outcomes, completed-work logs
- Temporary TODO state
- Stale artifacts: PR numbers, issue numbers, commit SHAs, artifact counts
- Anything likely to be invalid within 7 days
- Raw factual claims or retrieved source content

### 3. Read Before Write

- Before writing, read current memory to avoid duplicates and identify stale entries.
- If memory is near the character limit, consolidate stale entries first before adding new ones.

### 4. Write Shape

- Use declarative facts, not imperative instructions.
- Prefer compact entries that remain valid across sessions.
- Batch multiple changes in one call when possible.

### 5. Retention / Cleanup

- Memory is a compact operational store, not a changelog.
- After major changes (profile updates, skill additions, workflow changes), audit memory for stale entries.
- If an entry becomes advisory-only, move it to a skill or reference file instead of keeping it in memory.

## Procedure

### Read

1. Call `memory_tool` read for the relevant target (`memory` or `user`).
2. Inspect for entries that overlap with the new fact.
3. If duplicates exist, merge or replace rather than append.

### Write

1. Decide the target: `user` for profile/preferences, `memory` for operational facts.
2. Draft entry as a declarative fact.
3. If replacing existing content, identify a unique substring to target.
4. If adding and near limit, remove or shorten stale entries in the same batch.

### Test

1. After write, confirm the operation succeeded.
2. Verify no critical prior entry was unintentionally removed.
3. Confirm the new entry will still be valid in a future session.

## Pitfalls

- Do not store task progress or session outcomes.
- Do not treat memory as a long-form document; it is a compact fact store.
- Do not write entries that will become stale quickly.
- Do not bypass cleanup after major workflow changes.

## Verification

- Memory target was inspected before write
- New entry is declarative, compact, and durable
- No stale or duplicate entries remain
- Write operation succeeded and entry is present