# Profile Clone Behavior Notes

This reference captures observed behavior from `hermes profile create --clone-from dave <worker>` and the follow-up SOUL.md rewrite flow so future sessions do not re-discover it.

## Observed Behavior

- Hermes `profile create --clone-from dave ...` cloned the full SOUL.md/template set, including the **orchestrator** header/title and orchestration-specific tool guidance, instead of automatically applying the worker template.
- The cloned profile also inherited the **orchestrator worker list** (`librarian, roleplay, travis`) in `/home/degi/.hermes/profiles/dev-architect/SOUL.md`, even though `profiles list` still shows the legacy profiles.
- This means the owned profile can be cloned successfully but is **not deployment-ready** for worker operation until SOUL.md is corrected.

## Post-Clone Required Step

After clone:
1. Rewrite the new profile's `SOUL.md` from the worker template (`soul-core/templates/SOUL_TEMPLATE_V7.md`).
2. Replace all `{{PLACEHOLDER}}` values with profile-specific content.
3. Remove any orchestrator-scoped identity/title/tooling inherited from the source profile.
4. Preserve the worker skill trigger set only; do not leave `control-plane-management` or `task-decomposition` in a worker template.

## Implication for Workflow

- Do not treat `hermes profile create --clone-from dave <name>` as final state for a worker profile.
- Treat it as a **bootstrap step only**; the actual worker identity is applied through template rewriting.