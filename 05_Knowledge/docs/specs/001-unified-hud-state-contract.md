# Spec 001: Unified HUD State Contract & HUD Prototype

- Status: draft
- Owning repo: `reallife-spark/vrspark`
- Affected repos: `reallife-spark/vrspark`; Home pointer only for cross-repo visibility
- Source: `03_Sources/gemini/nreal-light-s24-xr-architecture-setup-guide.txt`
- Triage: `ready-for-agent` when published to GitHub Issues

## Problem Statement

The vrspark XR stack has multiple real-time producers — Nreal Light/S24 media and vision events, Neurosity Crown BCI metrics, Galaxy Watch Ultra biometrics, and NVIDIA DeepStream inference — but no single normalized contract for the HUD to consume. Without a canonical HUD state contract, each UI experiment would couple directly to vendor SDKs, transport details, and timing quirks, making prototypes brittle, hard to test, and unsafe to evolve as sensors/pipelines change.

## Solution

Define a **Unified HUD State Contract** as the primary integration seam: a canonical, versioned HUD state payload published at 10–15 Hz on `ui.hud.state`, consumed by a transparent Nreal Light HUD prototype. Producers emit normalized telemetry; a fusion/publisher component aligns samples by NTP timestamp and publishes the single HUD state. The first deliverable is a contract + HUD prototype that renders against mock or recorded contract-compliant payloads, with no direct vendor SDK dependency in the UI.

## User Stories

1. As an XR developer, I want a canonical HUD state schema, so that I can build HUD features without coupling to vendor SDK payloads.
2. As an XR developer, I want the HUD contract versioned, so that producers and UI can evolve without silent breakage.
3. As an XR developer, I want a single `ui.hud.state` feed at 10–15 Hz, so that the HUD has predictable render/update behavior.
4. As a HUD operator, I want the HUD to render on a pure-black background, so that black pixels remain optically transparent on Nreal Light.
5. As a HUD operator, I want critical HUD controls kept inside a 40px safe margin, so that birdbath edge softness does not hide important information.
6. As an XR developer, I want the HUD to display runtime/display mode status, so that I can confirm AR/MR, DeX HUD, or VR/SBS mode at a glance.
7. As an XR developer, I want the HUD to display device/stream health, so that I can diagnose stale or missing producer data.
8. As a BCI user, I want focus and calm values displayed as normalized percentages, so that I can understand current cognitive state quickly.
9. As a BCI user, I want Crown connection, battery, and signal-quality status visible, so that I can trust or troubleshoot BCI-driven behavior.
10. As a BCI user, I want kinesis trigger events surfaced with label and confidence, so that I can see when a hands-free command was detected.
11. As a biometric user, I want heart rate and HRV/IBI-derived status visible, so that I can monitor physiological state during XR sessions.
12. As a biometric user, I want skin temperature and accelerometer summary visible when available, so that I can correlate movement/thermal state with the session.
13. As a HUD operator, I want stale sensor values visually marked, so that I do not mistake old data for live data.
14. As a HUD operator, I want non-urgent notifications collapsed when focus is high (`focus > 0.70`), so that cognitive load is not increased during concentrated work.
15. As a HUD operator, I want collapsed notifications recoverable after focus drops or by explicit user action, so that I do not lose important information.
16. As an XR developer, I want vision/inference events represented as normalized HUD events, so that DeepStream outputs can be displayed without UI knowledge of GStreamer internals.
17. As an XR developer, I want audio/transcription or audio-event summaries representable in the contract, so that future audio pipelines can feed the same HUD.
18. As an accessibility-minded user, I want HUD text and status colors to have sufficient contrast against non-black surfaces used for badges/alerts, so that status is readable.
19. As an XR developer, I want the HUD prototype to run against mock payload fixtures, so that UI behavior can be tested without hardware.
20. As an XR developer, I want the HUD prototype to run against recorded payload replay, so that timing/staleness/load-shedding behavior can be tested deterministically.
21. As an XR developer, I want schema validation for contract payloads, so that malformed producer/fusion output is detected before reaching UI rendering logic.
22. As an XR developer, I want unknown/additive fields tolerated by the HUD, so that producers can add non-breaking data.
23. As an XR developer, I want required fields and enums explicitly documented, so that producer implementations are interoperable.
24. As a security-conscious developer, I want the contract to exclude secrets and raw personal health exports, so that HUD telemetry remains safe to log/debug in development.
25. As an XR developer, I want timestamps in a consistent NTP/epoch format, so that producers can be aligned and staleness computed.
26. As an XR developer, I want every producer section to include freshness metadata, so that the HUD can degrade gracefully per-source.
27. As a HUD operator, I want the HUD to indicate when it is disconnected from `ui.hud.state`, so that I can distinguish “no data” from “all systems normal.”
28. As an XR developer, I want the HUD layout to be multi-pane capable, so that future panes can be added without redesigning the contract.
29. As an XR developer, I want a minimal “debug overlay” mode, so that I can inspect raw contract metadata during development.
30. As a product owner, I want the first prototype scoped to contract + mock/replay HUD, so that value is delivered before full hardware fusion is available.

## Implementation Decisions

- **Primary seam**: `ui.hud.state` canonical HUD state contract. The HUD consumes only this contract; it must not directly import Neurosity SDK, Samsung Health SDK, DeepStream/GStreamer APIs, or Android UVC APIs.
- **Contract shape**: versioned envelope with `schemaVersion`, `timestamp`, `session`, `runtime`, `sources`, `bci`, `biometrics`, `events`, and `ui` sections. Additive fields are allowed; breaking changes require a schema version bump.
- **Update rate**: fusion/publisher targets 10–15 Hz. HUD tolerates missed frames and uses per-source staleness thresholds.
- **Transport abstraction**: contract is transport-agnostic at the schema level; initial reference transport is WebSocket or broker-backed websocket bridge. NATS/MQTT details are deferred to Spec 004 but must not change the HUD schema.
- **Freshness model**: each source includes `status` (`ok|stale|offline|error`), `lastSeenAt`, and optional `latencyMs`. HUD renders stale/offline states explicitly.
- **BCI mapping**: focus/calm are normalized 0–1 in contract and displayed as percentages; kinesis events include `label`, `confidence`, `occurredAt`, and `debounced` flag. Raw EEG is out of scope for this contract.
- **Biometrics mapping**: include heart rate, IBI/HRV summary, skin temperature, accelerometer summary, device status, and freshness. Raw health exports are out of scope.
- **Event model**: inference/audio/system events are normalized records with `id`, `kind`, `severity`, `occurredAt`, `source`, `summary`, and optional structured payload. HUD uses severity for collapse/priority behavior.
- **Load shedding**: when `bci.focus >= 0.70`, non-urgent notifications collapse by default; urgent/system-failure events remain visible. Threshold is configurable but defaults to 0.70.
- **HUD visual rules**: background `#000000`, safe margin 40px, high-contrast accent colors, no reliance on black for visible elements except transparency.
- **Prototype scope**: static/mock WebSocket feed or local fixture replay driving a multi-pane HUD; no direct hardware integration required.
- **Reference standards**: WebXR/WebVR sample structure and polyfill caveat-documentation style from `nreal-ai/webxr-samples` and `nreal-ai/webxr-polyfill` are reference patterns; NRSDK/MRTK/uvc_android are device-behavior references only, not implementation targets for this web HUD slice.

Decision-rich contract sketch (from design, trimmed):

```json
{
  "schemaVersion": "1.0.0",
  "timestamp": "2026-11-27T00:00:00.000Z",
  "session": {"id": "uuid", "mode": "deXHud|arMr|vrSbs"},
  "runtime": {"display": {"target": "nrealLight", "fps": 60}, "hudConnected": true},
  "sources": {
    "crown": {"status": "ok", "lastSeenAt": "..."},
    "watch": {"status": "stale", "lastSeenAt": "..."},
    "vision": {"status": "ok", "lastSeenAt": "..."}
  },
  "bci": {
    "focus": 0.82, "calm": 0.41,
    "signalQuality": {"overall": "good"},
    "kinesis": {"label": "leftHandPinch", "confidence": 0.91, "occurredAt": "..."}
  },
  "biometrics": {"heartRateBpm": 72, "hrvMs": 48, "skinTempC": 33.1},
  "events": [{"id": "e1", "severity": "info", "kind": "vision", "summary": "..."}],
  "ui": {"collapseNonUrgent": true, "safeMarginPx": 40}
}
```

## Testing Decisions

- **What makes a good test**: tests validate external behavior and contract compliance, not internal rendering framework details. Tests use fixture payloads/replay streams and assert visible HUD state, validation outcomes, and staleness/load-shedding behavior.
- **Modules to test**: schema validation, HUD state reducer/selector logic, staleness computation, focus-based notification collapse, HUD rendering of required sections, and disconnect/error states.
- **Prior art**: use `nreal-ai/webxr-polyfill` for test organization/caveat documentation patterns (mocha/rollup-era style is reference only; choose current repo-appropriate test tooling when implementing). Use simple fixture-driven UI tests similar to web sample app behavior checks; no Unity/NRSDK test harness in this slice.

## Out of Scope

- Direct Neurosity/Samsung Health/DeepStream SDK integration (covered by Specs 003/004).
- Native Android Nreal UVC capture implementation.
- Nebula/NRSDK 6DoF spatial anchoring.
- Persistent storage of biometric/EEG history beyond debug replay fixtures.
- Authentication/secret management for vendor clouds.

## Further Notes

- This spec is the highest-priority seam for vrspark. Specs 002–004 should produce or consume data compatible with this contract.
- The GitHub repo `reallife-spark/vrspark` is not yet created remotely; publish this spec as a GitHub issue when the repo exists, then apply `ready-for-agent`.
