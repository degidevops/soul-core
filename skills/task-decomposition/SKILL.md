---
name: task-decomposition
description: "Use when decomposing goals. Defines tuple rules and DAG."
version: 1.0.0
author: Maru (soul-gen project)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    category: soul-core
    tags: [orchestrator, decomposition, kanban, mandatory]
    related_skills: [control-plane-management, failure-mode-detection]
---

# Step 3: Task Decomposition — Atomic Task Breakdown with Tuple Concretization

## When to Use
- When decomposing high-level goals into atomic Kanban tasks for worker assignment
- When evaluating auto-decomposed task graphs in triage column
- When adjusting over/under-decomposed tasks

## Procedure

### Phase 1: Parse
1. Read the high-level goal from Step 0 Intent Contract
2. Identify key deliverables and acceptance criteria
3. Determine if the goal can be executed as a single task or requires decomposition

### Phase 2: Decompose
1. Break goal into atomic sub-tasks — each with a single, clear completion criterion
2. For each sub-task, concretize the Instruction-Context-Tools-Model tuple:
   - **Instruction:** What the worker must do (goal field in kanban_create)
   - **Context:** Constraints, input data, reference materials (context in task description)
   - **Tools:** Implicit via worker profile capabilities (assignee determines toolset)
   - **Model:** The worker profile name (assignee field in kanban_create)
3. Map dependencies between sub-tasks — what must complete before what?
4. Maximize parallelism: independent tasks should NOT have dependency links

### Phase 3: Validate
1. Every task MUST have a verified assignee profile (check assignee list — never assume)
2. Every task MUST have explicit acceptance criteria in the goal field
3. Every task MUST specify expected artifacts (files, reports, outputs)
4. Dependency graph must be a DAG — no circular dependencies
5. Granularity check: each task should be completable by a single worker in a single context window

### Tuple Concretization Rules

| Rule | Description |
|---|---|
| **Instruction Clarity** | Task goal must be self-contained and clear to a worker reading it for the first time. Include: what to produce, format, and quality bar. |
| **Context Boundaries** | Include only task-relevant context. Do not copy-paste conversational history (prevents Context Pollution). Reference files by path instead of embedding content. |
| **Tool Matching** | Assign tasks to profiles with matching tool capabilities. Verify tool-capability match before assignment. |
| **Model Selection** | Choose the most specific profile available. Specificity improves output quality. |
| **Workspace Selection** | Specify workspace type per task: scratch (default, isolated, deleted on completion), dir:<absolute-path> (shared directory, preserved on completion), worktree (git worktree, preserved on completion). Default to scratch unless shared access is needed. |

### Granularity Metrics

| Metric | Signs | Mitigation |
|--------|-------|-----------|
| **Over-decomposition** | Tasks produce only a few lines of output. Coordinator overhead > task execution time. | Merge related sub-tasks into larger atomic units. |
| **Under-decomposition** | Single task requires multiple disparate skills. Task goal is ambiguous or multi-faceted. Worker likely overwhelmed. | Split into skill-specific sub-tasks with clear interfaces. |
| **Target** | Each task = one worker, one skill domain, clear input→output mapping, completable without external coordination. | |

### Decomposition Output Format
```
Orchestration Plan for: [High-Level Goal]
Worker Profiles Verified: [list]

Task Graph (DAG):
├── Task 1: [Title]
│   Instruction: [What to do]
│   Context: [Constraints, inputs, references]
│   Model (Assignee): [profile-name]
│   Dependencies: none (root task)
│   Expected Artifacts: [file paths / deliverables]
│
├── Task 2: [Title]
│   Instruction: [What to do]
│   Context: [Constraints, inputs, references]
│   Model (Assignee): [profile-name]
│   Dependencies: Task 1
│   Expected Artifacts: [file paths / deliverables]
│
└── Task N: [Title] (Synthesis)
    Instruction: [Integrate all artifacts into final deliverable]
    Context: [Task 1..N-1 outputs]
    Model (Assignee): [orchestrator-self or synthesis-profile]
    Dependencies: Task 1, Task 2
    Expected Artifacts: [final deliverable path]
```

### Synthesis Trigger
When all non-synthesis tasks reach completed status:
1. Read all task artifacts via file_read (paths from kanban_show)
2. Cross-reference outputs for consistency (flag conflicts)
3. Apply verification: check completeness against original goal
4. If gaps → create new tasks for missing pieces
5. If conflicts → investigate root cause, request clarification via kanban_comment
6. Produce final coherent deliverable
7. Archive completed task chain

## Pitfalls
- Never assign to unverified profiles — verify before assignment
- Never create false dependencies — maximize parallelism
- Never copy-paste conversational history into task context — reference files by path
- Never skip granularity check — over/under decomposition both harm throughput

## Verification
- All assignee profiles verified
- Acceptance criteria explicit per task
- DAG has no cycles
- Granularity validated (one worker, one skill domain per task)
- Synthesis trigger executed when tasks complete
