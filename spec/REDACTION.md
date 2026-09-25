# Specification: On-Device Redaction Standards (v0.1)

## Principles

1. **Edge-first execution.** Redaction MUST execute on-device (or on a wearer-owned local hub) prior to any non-volatile disk serialization or network transmission of raw frames.
2. **Best-effort anonymization.** Visual masking models aim to obscure faces, computer monitors, and text documents. This is a mitigation, not a guarantee — implementations MUST NOT represent redaction as complete or certain to end users.
3. **Keypoint fallback.** When background visual risk is elevated (e.g., an unrecognized environment) or the task does not need appearance detail, raw RGB frames SHOULD be discarded in favor of extracted 3D hand/object keypoint trajectories.
4. **Fail closed.** If the redaction stage errors or cannot confirm it ran, the episode MUST be discarded or quarantined rather than saved unredacted.

## Known Limitations

- Automated face/screen/document detectors have non-zero false-negative rates; users should be given a review step before an episode is shared or used for third-party training.
- Redaction quality depends on device compute; latency/quality benchmarks for reference pipelines belong in `/redaction` and should be published honestly rather than assumed.
