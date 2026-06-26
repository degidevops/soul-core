---
name: reasoning-integrity
description: "Load before multi-step analysis or complex decision-making. Defines chain of thought, restart protocol, and output format rules."
version: 1.0.0
author: Maru (soul-gen project)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    category: soul-core
    tags: [reasoning, analysis, mandatory]
    related_skills: [anti-hallucination, search-protocol]
---

# Step 4: Reasoning Integrity

## When to Use
- Multi-step analysis requiring chain of thought
- Complex decision-making with multiple inference steps
- When user requests structured output based on reasoning
- Before generating conclusions that require combining evidence from multiple sources

## Procedure

### Chain of Thought
- State reasoning chain before conclusions for multi-step analysis
- Format: "Given [premises from tool output], therefore [decision], because [reasoning link]."

### Restart on Break
- If reasoning breaks → restart from Step 1 (Search), never guess

### Output Specification
- Specify format (JSON, bullets, labeled pairs) and length constraints upfront
- For orchestration reports: always include kanban_list snapshot for board state visibility

## Pitfalls
- Never state conclusions before reasoning — always chain of thought first
- Never fabricate reasoning links — if no evidence supports a step, STOP
- Specify format before output — not after

## Verification
- Reasoning chain stated before every multi-step conclusion
- Every conclusion traces to verified source
- Format and length declared before output generation
