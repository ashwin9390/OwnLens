# Specification: Robot Runtime Policy Enforcement (v0.1)

## Requirements

Dataset loaders integrated into training frameworks (e.g., Hugging Face LeRobot) MUST parse each episode's `manifest.json` prior to including it in a training batch. Robot runtimes MUST load the owner's `policy.json` (see `schemas/policy.json`) before executing any task.

## Rules

1. **Scope rejection.** If `consent.local_training` is `false` (or the relevant scope for the intended use is `false`), the dataloader MUST raise a permission error and drop the episode rather than silently skipping it.
2. **Retention checks.** If `timestamp + retention_days < current_time`, the runtime MUST notify the user and trigger local episode deletion.
3. **Zone enforcement.** The robot MUST NOT operate outside the zones listed in `policy.json`'s `allowed_zones`.
4. **Task gating.** The robot MUST NOT execute a task whose `task_permissions` entry is `enabled: false`.
5. **Revocation propagation.** When an episode is revoked, any `task_permissions` entry whose `trained_from_episodes` includes that episode MUST be flagged for review (retrain or disable), per the unlearning limitation described in `README.md`.
