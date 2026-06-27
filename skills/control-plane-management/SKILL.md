---
name: control-plane-management
description: "Use before orchestration. Defines Kanban protocols."
version: 1.0.0
author: Maru (soul-gen project)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    category: soul-core
    tags: [orchestrator, kanban, control-plane, mandatory]
    related_skills: [task-decomposition, failure-mode-detection]
---

# Step 2: Control Plane Management — Verification-Driven Coordination Loop

## When to Use
- Before making ANY orchestration decision involving task assignment or state changes
- Before responding to worker block signals
- After context compression events (re-anchor board state)

## Procedure

### Kanban State Protocol

**Board-First Visibility:** ALWAYS check kanban_list BEFORE making any orchestration decision. Never assume task state — read it from the board.

**Triage Column Handling:** Tasks in triage are auto-decomposed. Review decomposed child graphs for quality: validate profiles, correct granularity, and DAG dependencies. Promote to ready or adjust decomposition.

**Todo Column Awareness:** Tasks in todo wait on dependencies. Dispatcher auto-promotes to ready when dependencies resolve. Do not manually move todo tasks unless debugging dispatcher.

**Atomic State Transitions:** All task state changes MUST go through kanban_* tools. Direct DB manipulation is prohibited. Dispatcher handles atomic claims (ready → claimed) to prevent race conditions.

**Zombie Detection & Reaping:** Flag tasks in claimed status beyond expected execution times without heartbeat as zombies. Perform periodic audits. Dispatcher blocks tasks automatically after consecutive spawn failures.

### Block Response Protocol
1. Read block reasons via kanban_show (do not assume)
2. Diagnose root causes: missing info, dependency issues, capability mismatches, resource bounds
3. Choose intervention: add context and unblock, reassign profiles, redecompose task, or escalate to user
4. Execute intervention; never ignore block signals
5. Verify intervention success via kanban_list checks

### Verification-Driven Replanning (after kanban_complete)
1. Read task artifacts via kanban_show + file_read
2. Verify result completeness against task goals
3. Create children tasks for detected gaps
4. Resolve conflicts with sibling tasks; query clarifications via kanban_comment if unresolved
5. Reject and redecompose if output quality is insufficient

### Adaptive Topology Optimization
Evolve orchestration strategy dynamically based on task complexity and worker availability. For simple, independent tasks → maximize parallelism (fan-out). For complex, interdependent tasks → sequentialize critical path, parallelize independent branches. Evaluate each task's structure independently rather than using static patterns.

### Dispatcher Coordination
- **Orchestrator responsibility:** Create tasks with Instruction-Context-Model tuples, set dependency links, monitor board state, diagnose blocks, verify completions, synthesize outputs
- **Dispatcher responsibility:** Atomic claim (ready → claimed), spawn assigned worker profiles, detect missing heartbeats (zombie detection), reclaim stale claims, auto-block after failure limit
- **Key insight:** The orchestrator does NOT directly spawn workers — it creates tasks with assignee profiles and lets the dispatcher handle spawning. Do not attempt to bypass the dispatcher through delegate_task for task execution.

### Loop Termination Rule

- Within a single orchestration pass, do not alternate between `task-decomposition` and `control-plane-management` more than three times without producing a board state change.
- If no task is created, updated, or unblocked after three cycles, halt the loop and escalate to the user with:
  - current board snapshot from `kanban_list`
  - last decomposition or intervention attempted
  - diagnosed blocker
- Replan only after explicit user direction or after a worker status change appears on the board.

### Tenant Isolation
Scope all operations strictly to HERMES_KANBAN_BOARD. Tenant is a soft namespace for path and memory isolation. Orchestrator MUST NOT create tasks on boards outside its assigned tenant. Cross-tenant coordination requires explicit user authorization.

## Pitfalls
- Never directly manipulate kanban.db SQL — always use kanban_* tools
- Never bypass Kanban lifecycle — state changes only through tools
- Never ignore block signals — diagnose and intervene
- Never assume task state — always read from board
- Never bypass dispatcher via delegate_task for task execution

## Verification
- kanban_list checked before every decision
- Block reasons diagnosed before intervention
- Board state re-anchored after compression
- Dispatcher not bypassed for task execution
- Orchestration loop terminates after three cycles without board state change
- Termination events include board snapshot and blocker diagnosis
