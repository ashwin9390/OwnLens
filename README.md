# OwnLens

> **Your glasses. Your robot. Your rules.**
> *An open, user-owned egocentric data & permission specification for privacy-conscious personal robot learning.*

---

## Overview

**OwnLens** is an open specification and software architecture designed to help make smart-glasses-based robot teaching more private, customizable, and user-controlled.

While frameworks like Hugging Face [LeRobot](https://github.com/huggingface/lerobot) make robot policy training hardware-agnostic, egocentric video streams can inadvertently capture sensitive surroundings and private interactions.

---

## Status

**Current Version:** `v0.1-alpha` (Open Specification & Defensive Publication)

> **Important Notice & Disclaimers**
> - **Not legal advice.** This specification and its schemas are technical privacy tools, not a substitute for legal compliance advice across jurisdictions.
> - **Redaction is best-effort.** Automated computer-vision redaction reduces exposure; it is not a guarantee against visual data leakage. Review before sharing.
> - **Hardware trust assumed.** OwnLens assumes the device OS running capture and redaction is not itself compromised or tampered with; it does not defend against malicious hardware or firmware.
> - **Unlearning is limited.** Revoking consent for an episode reliably prevents *future* training runs on it and (where policies are split per task — see Architecture Notes below) can remove a task from future training pipelines, but it does not guarantee removal from every previously trained or merged model artifact.

---

## Why OwnLens?

Egocentric learning systems (e.g., EgoMimic, EgoZero, EMMA, EgoVerse, EgoKit) rely on continuous first-person demonstration data. Existing collection paradigms are often organized around enterprise-scale data pipelines and consent practices that are not aligned with personal, local, or user-controlled robotics.

OwnLens proposes a **local-first, user-owned architecture**:

| Typical Egocentric Capture Practices | OwnLens Proposed Specification |
| :--- | :--- |
| **Often binary:** simple record on/off toggles | **Context-aware:** rule-based by room, task, person, or time |
| **Often cloud-first:** raw visual data uploaded directly | **On-device minimization:** aims to strip sensitive visual features at capture |
| **Monolithic dataset terms:** one agreement covers all uses | **Permission manifests:** machine-readable consent attached per episode |
| **Often irrevocable in practice:** hard to unwind after the fact | **Purpose-limited scopes:** separates local training, sharing, and third-party training rights |

---

## Core Architecture

```text
  [ Smart Glasses / Sensor Device ]
                 │ (Raw first-person stream: video + audio + IMU)
                 ▼
  ┌─────────────────────────────────────────────────────────┐
  │ 1. Hardware Adapter & Normalizer                         │
  └──────────────────────────┬──────────────────────────────┘
                              ▼
  ┌─────────────────────────────────────────────────────────┐
  │ 2. On-Device Redaction Engine                            │
  │    (Face blur, screen/document masking, keypoint extraction) │
  └──────────────────────────┬──────────────────────────────┘
                              ▼
  ┌─────────────────────────────────────────────────────────┐
  │ 3. Manifest Generator & Policy Attachment                │
  │    (Attaches machine-readable consent & scope metadata)  │
  └──────────────────────────┬──────────────────────────────┘
                              ▼
  ┌─────────────────────────────────────────────────────────┐
  │ 4. Robot Runtime & Dataset Loader                        │
  │    (Enforces local policy, rejects disallowed episodes)  │
  └───────────────────────────────────────────────────────────┘
```

- **Vendor-agnostic capture adapter** — designed to support research egocentric platforms (e.g., Project Aria) and consumer devices where raw-stream access is available, via a standardized sensor interface.
- **Context-aware rules** — policy definitions can use spatial signals, paired markers, or visual tags to auto-pause or restrict capture in designated areas (e.g., bathrooms, restricted desks).
- **On-device feature minimization** — local models blur faces, mask monitors, and strip identifying detail before anything is saved to disk; converts raw video into hand/object keypoint trajectories and other privacy-preserving features.
- **Purpose-limited consent scopes** — separates capture rights from local training, multi-robot sharing, third-party model contributions, and retention lifecycle.
- **LeRobot-compatible permission manifests** — lightweight JSON metadata alongside standard robot dataset formats.
- **Robot-side policy enforcement** — the robot runtime validates each episode's manifest before fine-tuning or memory ingestion, refusing out-of-bounds or revoked data.
- **Provenance & audit trail** — tracks which episodes contributed to which policy updates.

### Architecture note: per-task policy splitting

One design option worth calling out explicitly: keep a general base robot policy frozen, and train each user-specific task as a small, separate adapter (e.g., a LoRA-style module) rather than merging all user data into one monolithic policy.

---

## Permission Manifest (v0.1)

Each episode captured via OwnLens generates a `manifest.json` file stored alongside the dataset trajectories:

```json
{
  "$schema": "https://ownlens.org/schemas/v0.1/manifest.json",
  "manifest_version": "0.1",
  "episode_id": "ep_2026_0921_8f9a12",
  "timestamp": "2026-09-21T18:28:00Z",
  "capture_device": {
    "type": "smart_glasses",
    "vendor": "generic_adapter",
    "firmware": "v1.2.0"
  },
  "context": {
    "place": "kitchen",
    "task": "load_dishwasher",
    "geofence_id": "home_zone_primary"
  },
  "redaction": {
    "faces": "blurred_on_device",
    "screens": "masked_black",
    "documents": "masked_black",
    "audio": "stripped",
    "output_mode": "keypoint_and_rgb"
  },
  "consent": {
    "local_training": true,
    "share_with_peers": false,
    "third_party_foundation_training": false,
    "retention_days": 90,
    "revocable": true
  },
  "provenance": {
    "sha256_hash": "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855",
    "signature": "PLACEHOLDER-REPLACE-WITH-REAL-SIGNATURE"
  }
}
```

> The `signature` field above is illustrative only — do not copy it into a real manifest. It's a placeholder for a device- or user-key signature (e.g., DER-encoded ECDSA) that would let a verifier confirm provenance and integrity.

See [`schemas/manifest.json`](schemas/manifest.json) for the formal schema, and [`schemas/policy.json`](schemas/policy.json) for the robot-side policy schema referenced in `spec/RUNTIME.md`.

---

## Repository Layout

Current and planned project structure:

```text
OwnLens/
├── spec/               # Formal specification, threat model, & RFCs
│   ├── MANIFEST.md     # Permission manifest schema spec
│   ├── REDACTION.md    # On-device processing standards
│   └── RUNTIME.md      # Robot-side policy enforcement spec
├── schemas/            # JSON Schema definitions (v0.1)
│   ├── manifest.json
│   └── policy.json
├── adapters/           # Hardware capture adapters (planned)
├── redaction/          # Reference redaction pipelines (planned)
├── lerobot-ext/        # Hugging Face LeRobot dataset extension (planned)
├── docs/               # Threat model and FAQ (planned)
└── paper/              # Defensive-publication / arXiv draft
```

---

## Roadmap

- [x] Draft initial core ideas and architecture
- [ ] Freeze manifest schema v0.1 (`schemas/manifest.json`)
- [ ] Freeze policy schema v0.1 (`schemas/policy.json`)
- [ ] Publish core threat model & privacy boundary guide (`docs/`)
- [ ] Build phone/smart-glasses mock capture adapter
- [ ] Develop a lightweight on-device anonymization pipeline (face + screen masking)
- [ ] Build a LeRobot dataset loader that rejects disallowed episodes
- [ ] Demonstrate end-to-end recording → LeRobot fine-tuning
- [ ] Benchmark real-time redaction latency on edge accelerators
- [ ] Usability study of permission defaults

---

## Related Work & Ecosystem

- **Robot learning frameworks:** Hugging Face [LeRobot](https://github.com/huggingface/lerobot)
- **Egocentric frameworks & datasets:** EgoMimic, EgoZero, EMMA, EgoVerse, EgoKit, Ego4D

Full citations are in [`paper/`](paper/).

---

## Non-Goals

- OwnLens is not a legal-compliance product and makes no jurisdiction-specific guarantees.
- OwnLens does not defend against tampered or malicious capture hardware.
- OwnLens does not claim to solve neural-network unlearning for jointly-trained models (see Status disclaimers above).

---

## Maintainers

- Ashwin H — [@ashwin9390](https://github.com/ashwin9390)

## License

- **Code and implementations:** [Apache License 2.0](LICENSE) (includes an explicit patent grant)
- **Specification and documentation:** [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/)

## Contributing

We welcome contributions from roboticists, privacy engineers, open-source maintainers, and security researchers. See [`CONTRIBUTING.md`](CONTRIBUTING.md) to get started.
