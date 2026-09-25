# OwnLens v0.1.0 — Initial Specification Release

**Your glasses. Your robot. Your rules.**

This is the first public release of OwnLens: an open specification for user-owned, permissioned egocentric teaching of personal robots. It is a **specification-only** release — no reference code ships yet.

## What's in this release

- A permission architecture for teaching robots from smart-glasses (or any wearable/phone) video, built around context-aware rules rather than a single record on/off toggle.
- A machine-readable **permission manifest** (`schemas/manifest.json`) attached to every recorded episode, separating capture, local-training, sharing, third-party-training, and retention consent.
- A robot-side **policy schema** (`schemas/policy.json`) so a robot enforces its owner's rules before acting on or training from data.
- Formal specs for the manifest, on-device redaction principles, and runtime enforcement (`spec/`).
- A defensive-publication paper (`paper/main.tex`, prebuilt as `paper/main.pdf`) covering related work, a threat model, the architecture, and open problems.

## What's explicitly *not* in this release

- No reference capture adapter, redaction pipeline, or LeRobot integration yet (`adapters/`, `redaction/`, `lerobot-ext/` are empty scaffolding).
- No claim that automated redaction is perfect — see the Status section in `README.md`.
- No solution to unlearning from a jointly-trained model — only the per-task-adapter design pattern offers a practical (partial) answer.

## Why this release exists

Egocentric video is already being used to train robots (EgoMimic, EgoZero, EMMA, and others), but almost entirely through platforms that collect data from workers or contributors under one-time consent. OwnLens proposes the missing piece: letting an individual teach their *own* robot their *own* tasks while keeping fine-grained, revocable control over what's captured and how it's used.

This release, and the accompanying paper, are published openly (Apache-2.0 / CC-BY-4.0) as a defensive disclosure, so these mechanisms stay available to the open community.

## Get involved

See `CONTRIBUTING.md`. The most useful contributions right now are in `spec/` and `schemas/` (schema review, edge cases) and a first reference capture adapter.

**Full changelog:** see `CHANGELOG.md`.
