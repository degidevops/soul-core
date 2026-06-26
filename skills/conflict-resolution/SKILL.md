---
name: conflict-resolution
description: "Load when external sources contradict each other or internal knowledge. Defines 3-priority rule set and output format for presenting conflicting evidence."
version: 1.0.0
author: Maru (soul-gen project)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    category: soul-core
    tags: [shared, conflict, evidence, verification, mandatory]
    related_skills: [search-protocol, anti-hallucination]
---

# Step 2: Conflict Resolution

## When to Use
- External content contradicts internal parametric knowledge
- Multiple external sources disagree on the same claim
- Worker outputs conflict with each other (orchestrator context)
- Board state contradicts orchestrator assumptions (orchestrator context)

## Procedure

### Priority Rules (strict order)

1. **External content ALWAYS wins** over internal knowledge — no hedging
2. **Flag explicitly:** "Note: This contradicts my training data. Using current external sources."
3. **If external sources conflict** → present all sides with citations; do not silently pick one
4. **If worker outputs conflict** → investigate root cause; request clarification; escalate if unresolvable
5. **If board state contradicts assumptions** → trust the board (it is the operational truth source); update plan accordingly

### Output Format for Source Conflicts

```
Claim: [the disputed fact]
Source A ([authority level]): [value X]
Source B ([authority level]): [value Y]
Resolution: [synthesis OR "unresolved — flagged for user review"]
```

## Pitfalls
- Never hedge external-over-internal as "it depends"
- Never silently pick one conflicting source — present all
- Never cite internal knowledge when external evidence contradicts it
- Never ignore board state in orchestrator context — board is operational truth

## Verification
- External evidence used as ground truth over internal
- All conflicting sources presented with citations
- Resolution explicitly stated or flagged as unresolved
