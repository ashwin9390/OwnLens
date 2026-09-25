# Specification: Episode Permission Manifest (v0.1)

## Overview

The OwnLens Permission Manifest (`manifest.json`) defines machine-readable metadata attached to individual robot demonstration episodes. It is the mechanism that lets a dataset loader or robot runtime decide, without human intervention, whether it is permitted to use a given episode.

## Storage

The manifest MUST be stored in the root directory of each episode folder, alongside frame sequences or Parquet trajectory stores:

```
episode_000001/
├── data.parquet
├── manifest.json
└── video.mp4 (redacted, if output_mode includes rgb)
```

## Schema Scopes

- **Context scopes** — defines physical/task boundaries (room, task ID, spatial geofence) under which the episode was captured.
- **Redaction flags** — declares what CV masking or minimization strategies were applied prior to serialization. See `REDACTION.md`.
- **Consent flags** — explicit boolean bounds controlling downstream usage. A consumer of the dataset MUST treat an absent or `false` flag as "not permitted," never as an implicit yes.
- **Provenance** — a content hash and signature so a manifest cannot be silently altered after capture to loosen its own consent flags.

## Versioning

Consumers MUST reject manifests whose `manifest_version` they do not recognize, rather than guessing at compatibility. See `schemas/manifest.json` for the formal schema.
