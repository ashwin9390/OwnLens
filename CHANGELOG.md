# Changelog

All notable changes to OwnLens will be documented in this file.
This project uses [Semantic Versioning](https://semver.org/) for the specification (`spec/`, `schemas/`); reference code, once added, will version independently.

## [0.1.0] — 2026-09-22

### Added
- Initial public specification: context-aware permission engine, on-device redaction principles, purpose-limited consent scopes, permission manifest format, robot-side policy enforcement, and provenance/revocation model.
- `schemas/manifest.json` — episode-level permission manifest schema (v0.1).
- `schemas/policy.json` — robot-side owner policy schema (v0.1).
- `spec/MANIFEST.md`, `spec/REDACTION.md`, `spec/RUNTIME.md` — formal specification documents.
- `paper/main.tex` — defensive-publication / arXiv draft, with related work, threat model, architecture, discussion, and open problems.
- Design note on per-task policy splitting (adapter-per-task) as a practical, partial answer to the unlearning limitation.
- Apache-2.0 license for code, CC-BY-4.0 for specification and documentation.

### Known limitations (see README Status section)
- Redaction is best-effort, not guaranteed.
- No defense against tampered capture hardware/firmware.
- Unlearning from a jointly-trained model is an open problem; only per-task adapters can be reliably "forgotten."
- `adapters/`, `redaction/`, `lerobot-ext/`, and `docs/` are empty scaffolding — no reference implementation ships in this release.

### Notes
- This is a specification-only release. There is no working code yet — contributions are welcome per `CONTRIBUTING.md`.
