# V7.6.4 — Mode 9: Skill Load Failure (Improvised Protocol)

**Date:** 2026-06-30
**Trigger:** User asked "Cara mengatasi agent tidak melakukan improvised protocol jika skill_view gagal."

## Problem

When `skill_view(name="...")` returns empty or error, the agent may attempt to reconstruct the protocol from internal parametric knowledge. This produces confident but ungrounded execution — structurally indistinguishable from hallucination. The agent says "I will follow the standard procedure..." without having actually loaded the procedure.

## Root Cause

Three gaps in the V7.6.3 architecture:
1. No explicit prohibition against improvising protocols in Hard Guardrails
2. No failure mode for "skill load failure" in `failure-mode-detection`
3. No protocol provenance check in `anti-hallucination` pre-delivery gate

## Solution: Three-Layer Defense

### Layer 1 — Hard Guardrail #4 (Templates)
Added to both Worker and Orchestrator templates:
> "Never execute a protocol if the corresponding skill_view call fails or returns no content. STOP immediately and report the missing skill. Improvised protocols are a Critical Failure (Mode 9: Skill Load Failure)."

### Layer 2 — Mode 9 (failure-mode-detection)
New critical failure mode:
- **Title:** Skill Load Failure
- **Behavior:** mandatory skill_view returns empty/error — agent attempts to improvise
- **Retry:** hard_stop_and_report
- **Severity:** critical
- **Response:** HARD STOP + emit standard failure report template

### Layer 3 — Check #5 Protocol Provenance (anti-hallucination)
Expanded 4-check to 5-check protocol:
> "All procedural steps taken in this response MUST trace to a successfully loaded skill_view output within this session. If a mandatory skill failed to load and the protocol was executed anyway → GATE FAILURE."

## Files Modified

| File | Change |
|------|--------|
| `templates/SOUL_TEMPLATE_V7.md` | +Hard Guardrail #4 |
| `templates/SOUL_TEMPLATE_ORCHESTRATOR_V76.md` | +Hard Guardrail #4 |
| `skills/failure-mode-detection/SKILL.md` | +Mode 9, updated description, detection guidelines, required actions, pitfalls |
| `skills/anti-hallucination/SKILL.md` | 4-check → 5-check, +Check #5 Protocol Provenance, updated description/pitfalls/verification |
| `references/CHANGELOG.md` | +v7.6.4 section |
| `SKILL.md` (umbrella) | +Pitfall #18 Improvised Protocol |

## Verification

- All 5 files patched and committed (bc2661f)
- `grep -rn "Mode 9" skills/failure-mode-detection/SKILL.md` → found
- `grep -rn "5-Check" skills/anti-hallucination/SKILL.md` → found
- `grep -rn "Hard Guardrail #4" templates/` → found in both templates
