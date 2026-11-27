# Spec 004: Multimodal Fusion Pipeline

- Status: draft
- Owning repo: `reallife-spark/vrspark`
- Affected repos: `reallife-spark/vrspark`; may coordinate with infrastructure/runtime repos if deployed outside this repo
- Source: `03_Sources/gemini/nreal-light-s24-xr-architecture-setup-guide.txt`
- Triage: `ready-for-agent` when published to GitHub Issues

## Problem Statement

vrspark needs to combine Nreal Light UVC video/audio, Galaxy Watch Ultra biometrics, and Neurosity Crown BCI into a synchronized real-time HUD. Direct browser-to-many-device connections create timing drift, bandwidth waste, fragile coupling, and poor observability. Without a fusion spine, the HUD cannot reliably correlate “what the user sees/hears,” “what the user intends,” and “what the user’s body state is.”

## Solution

Specify a broker-centered multimodal fusion pipeline using NATS JetStream or MQTT for telemetry transport and NVIDIA DeepStream as the video/audio inference and synchronization spine. Producers publish normalized, NTP-timestamped events; DeepStream pad probes align nearest BCI/biometric samples to frame metadata and emits a fused HUD state at 10–15 Hz conforming to Spec 001.

## User Stories

1. As an XR developer, I want a single fusion architecture, so that video, audio, BCI, and biometrics can be correlated consistently.
2. As an XR developer, I want telemetry transported through a broker, so that producers and consumers decouple cleanly.
3. As an XR developer, I want NTP-synchronized timestamps across devices, so that cross-modal alignment is possible.
4. As a HUD user, I want fused state updates at 10–15 Hz, so that HUD remains responsive without excessive bandwidth.
5. As an XR developer, I want Nreal UVC video/audio ingested through DeepStream-compatible transport, so that visual/audio inference can run on GPU.
6. As an XR developer, I want Watch biometrics relayed from watch to phone to broker, so that wearable data reaches fusion without direct HUD coupling.
7. As an XR developer, I want Crown BCI relayed through a bridge to broker, so that processed EEG metrics join the same timeline.
8. As an XR developer, I want nearest-sample alignment by NTP timestamp, so that HUD frames show temporally relevant BCI/biometrics.
9. As an XR developer, I want alignment/skew metrics exposed, so that I can detect clock drift or stale joins.
10. As an XR operator, I want producer outages to degrade gracefully, so that HUD continues with partial data.
11. As a security-conscious developer, I want broker topics/auth/config externalized, so that no secrets/credentials are committed.
12. As an XR developer, I want normalized topic schemas, so that producers can be replaced independently.
13. As an XR developer, I want DeepStream metadata attachment encapsulated, so that HUD never depends on GStreamer internals.
14. As an XR developer, I want fusion output to conform to Spec 001, so that HUD prototype and fusion evolve independently.
15. As a performance engineer, I want backpressure/drop policy defined, so that slow consumers do not destabilize producers.
16. As a QA tester, I want replayable fixture streams for video metadata, BCI, and biometrics, so that fusion logic can be tested without full hardware.
17. As an XR operator, I want a diagnostics view/feed for producer freshness and fusion latency, so that field issues are observable.
18. As a developer, I want Android 15/16+ camera permission constraints documented/handled, so that UVC capture startup is reliable.
19. As a product owner, I want an initial spike that fuses mock/recorded streams before live hardware, so that risk is reduced incrementally.
20. As an XR developer, I want schema evolution rules, so that new sensors can be added without breaking existing HUD consumers.

## Implementation Decisions

- **Primary seam alignment**: fusion service consumes normalized telemetry topics and emits only Spec 001 `ui.hud.state`; HUD has no direct broker/DeepStream dependency.
- **Broker**: NATS JetStream or MQTT acceptable; abstract broker interface in code/spec. Topic namespace defaults: `telemetry.bci.crown`, `telemetry.watch.biometrics`, `media.nreal.events`, `ui.hud.state`, `ops.fusion.health`.
- **Time sync**: all producers attach NTP epoch timestamps; DeepStream `attach-sys-ts=1`; fusion records skew and alignment delta.
- **DeepStream role**: ingest media (`nvurisrcbin`/equivalent), run inference (`nvinfer`/`nvinferaudio` as needed), attach aligned BCI/biometric context via pad probe/NvDsUserMeta, publish fused state through `nvmsgconv`/`nvmsgbroker` or equivalent bridge.
- **Biometrics path**: Wear OS app uses Samsung Health Sensor SDK to read HR continuous + IBI/HRV, skin temp continuous, accelerometer continuous; relay via MessageClient/DataClient to S24 app; S24 publishes normalized telemetry to broker.
- **BCI path**: Neurosity bridge publishes processed metrics/events (Spec 003), not raw EEG by default.
- **Alignment policy**: nearest-sample within configurable window; if outside window, mark source stale/missing for that frame and expose alignment miss metric.
- **Graceful degradation**: fusion emits partial HUD state with per-source status; no single producer failure stops `ui.hud.state`.
- **Security/ops**: secrets in env/secret manager; no credentials in repo; include topic ACL/TLS expectations in deployment docs.
- **Reference standards**: nreal-ai `uvc_android` informs UVC capture/metadata constraints; web repos inform lightweight client consumption patterns; NRSDK/MRTK are not fusion-stack targets.

## Testing Decisions

- **What makes a good test**: tests validate external fusion behavior — schema-compliant output, alignment decisions, staleness/degradation, rate limiting, and replay determinism — using recorded/mock streams.
- **Modules to test**: timestamp alignment/windowing, per-source staleness, fusion reducer/output schema, broker adapter contract, health/metrics emission, and partial-failure handling.
- **Prior art**: Spec 001 fixture/replay tests; DeepStream/message-broker integration tests should be isolated behind adapter interfaces with mock brokers and recorded frame metadata fixtures.

## Out of Scope

- Production Kubernetes/edge deployment hardening beyond basic runbook notes.
- Training ML models for vision/audio inference.
- Raw EEG/high-frequency raw sensor archival platform.
- Vendor cloud account provisioning.

## Further Notes

- Initial implementation should start with replayed/fixture inputs producing Spec 001-compliant `ui.hud.state` before connecting live Nreal/Watch/Crown hardware.
- This is the highest infrastructure-risk spec; keep it behind the stable HUD contract.
