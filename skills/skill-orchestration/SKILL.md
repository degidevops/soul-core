---
name: skill-orchestration
description: "Use when multiple mandatory skills fire together. Defines precedence, composition, and termination rules."
version: 1.0.0
author: Maru (soul-gen project)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    category: soul-core
    tags: [meta, orchestration, precedence, governance]
    related_skills: [context-hygiene, tool-use-discipline, anti-hallucination, human-in-the-loop, control-plane-management, task-decomposition]
---

# Skill Orchestration — Precedence & Composition

## When to Use

- Two or more mandatory skills could fire in the same turn.
- After loading one skill and before loading the next.
- Before resolving a failure mode that can re-trigger other skills.

## Precedence Rules

Apply skills in this order when multiple apply. Lower number = higher priority.

1. `human-in-the-loop`
2. `failure-mode-detection`
3. `anti-hallucination`
4. `context-hygiene`
5. `search-protocol`
6. `conflict-resolution`
7. `execution-provenance`
8. `reasoning-integrity`
9. `intent-validation`
10. `tool-use-discipline`
11. `control-plane-management` / `task-decomposition`

### Rationale

- Safety and hard failures block action before verification.
- Context saturation degrades all subsequent reasoning, so `context-hygiene` ranks above execution.
- Verification and evidence handling should occur before reasoning.
- Orchestration-specific skills remain last because they apply only after the request is valid and verified.

## Composition Rules

- If a skill's procedure explicitly hands off to another skill, follow the handoff rather than restarting the precedence chain.
- Do not load the same skill twice in one turn.
- After `context-hygiene` resets context, re-evaluate only the triggering skill and any safety/verification skills that may have changed state.
- Do not chain more than three skill loads in a single turn. If more are needed, split across turns.

## Termination Rule

- If precedence resolution consumes more than three iterations without producing a single deliverable action, halt and clarify with the user.
- Failure loops must terminate within two skill cycles. If not, escalate with the current diagnosis from `failure-mode-detection`.

## Pitfalls

- Do not invent precedence not documented here.
- Do not use this skill to bypass a required skill; it only orders them.
- Do not mark delivery complete until the highest-priority blocking skill is satisfied.

## Verification

- The highest-priority applicable skill was loaded first.
- No required skill was skipped.
- The skill chain terminated within the allowed iteration limit.