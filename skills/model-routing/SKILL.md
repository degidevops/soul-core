---
name: model-routing
description: "Use when selecting, comparing, or validating models or providers for a task or agent. Defines resolution, fallback, conflict handling, and evidence requirements."
version: 1.0.0
author: Maru (soul-gen project)
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    category: soul-core
    tags: [models, routing, providers, configuration]
    related_skills: [search-protocol, conflict-resolution, anti-hallucination]
---

# Model Routing — Selection, Fallback, and Validation

## When to Use

- Before assigning or changing a model/provider for an agent, profile, cron job, or task
- When current model has unsupported capabilities for the requested task
- When previous provider/model has failed or returned degraded output
- When user requests model/provider recommendation or migration
- When comparing local vs remote inference options

## Rule Categories

### 1. Claim Requirements

- Any statement about model/provider capability, version, pricing, or availability must come from retrieved, verifiable sources.
- Unverified claims about models or providers are forbidden in delivery.
- If two sources disagree, treat result as `CONFLICTING` and surface both with citations.

### 2. Selection Order

- Primary: **Pre-configured provider/model** that matches the task requirements and is known to be available in this environment.
- Secondary: **OpenRouter-compatible model** if the primary provider does not expose the required capability.
- Tertiary: **Local fallback** if remote providers are unavailable or cost-prohibitive.
- Do not bypass configured providers unless explicitly requested or unavailable.

### 3. Runtime Changes

- Changes require explicit confirmation.
- Confirmations must include: model identifier, provider identifier, task scope, conditions for rollback.
- Routing changes for destructive/high-stakes tasks require additional human-in-the-loop confirmation.

### 4. Custom Providers

- Custom providers must be documented and validated with a working request before use.
- If the user asks for a provider that is not configured, verify its actual availability and feasibility before adoption.

### 5. Local vs Remote Decision

- Prefer local when: offline operation, privacy-sensitive data, reproducibility, or cost control.
- Prefer remote when: higher capability required, specialized multimodal support, or SLA guarantees.
- Mixed routing (local for control/operations, remote for reasoning) is valid if task decomposition separates concerns.

## Procedure

### 1. Identify Constraints

- Task: input/output modality, context length, latency, reliability, cost
- Environment: network access, hardware, configured providers
- Policy: must-match provider support list; no unsupported services

### 2. Evidence Search

- Search official docs, pricing pages, capability matrices for candidate models.
- Cross-reference with at least one independent source for critical claims.
- If no source resolves provider availability, flag as `UNVERIFIED`.

### 3. Present Options

- Provide options with: model, provider, cost, capability match, fallback behavior, and rollback.
- Do not recommend without citing evidence.
- Structure the recommendation as evidence-backed, not opinion-based.

### 4. Confirm and Record

- Do not apply routing changes until confirmation.
- Record the decision in provenance or session log.
- After change, validate with a lightweight test call before treating it as active.

## Examples

- **Example 1:** User wants to run long-context reasoning. Search current provider docs for context window; compare to OpenRouter alternatives; propose 1 primary + 1 fallback with reasoning.
- **Example 2:** Cron job fails on model quota. Search failure cause and alternative; propose new model with cost/capability trade-offs; confirm with user.

## Pitfalls

- Do not treat internal parametric knowledge as current capability/pricing.
- Do not select models based on popularity or recency alone; match to task.
- Do not apply provider changes without confirmation.
- Do not skip capability verification before assignment.

## Verification

- Model/provider claim cited or flagged
- Options presented with evidence
- User confirmation obtained before change
- Post-change validation test executed
- Provenance status recorded for the decision