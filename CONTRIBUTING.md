# Contributing to OwnLens

Thank you for your interest in contributing to OwnLens!

OwnLens is an open, community-driven specification and architecture for user-owned, permissioned egocentric robot teaching. We welcome contributions from developers, security researchers, roboticists, and privacy advocates.

## How You Can Contribute

- **Specification & schemas** (`/spec`, `/schemas`) — help refine the manifest and policy schemas, propose new consent scopes, or improve context/geofencing definitions.
- **Hardware adapters** (`/adapters`) — build interface adapters for devices with available raw-stream access (verify SDK terms before integrating any specific vendor's hardware).
- **Redaction pipelines** (`/redaction`) — build or optimize edge-compatible computer-vision models for face, screen, and document masking, and publish honest benchmarks (latency, false-negative rate) rather than assuming perfect redaction.
- **LeRobot integration** (`/lerobot-ext`) — write dataloader wrappers that validate episode manifests during training data ingestion.
- **Threat modeling & docs** (`/docs`) — help stress-test the security model and write user-facing explanations.

## Development Process

1. Fork the repository and create your branch from `main`:
   ```bash
   git checkout -b feature/my-new-feature
   ```
2. Open a pull request with a clear summary of changes and use cases.
3. For anything touching the manifest or policy schema, please open an issue first to discuss the change, since downstream tooling depends on schema stability.
