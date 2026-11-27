# Spec 003: Neurosity Crown BCI Integration

- Status: draft
- Owning repo: `reallife-spark/vrspark`
- Affected repos: `reallife-spark/vrspark`
- Source: `03_Sources/gemini/nreal-light-s24-xr-architecture-setup-guide.txt`
- Triage: `ready-for-agent` when published to GitHub Issues

## Problem Statement

Neurosity Crown can provide high-value cognitive and intent signals for XR workflows, but browser/WebView constraints, raw EEG bandwidth, noisy signal quality, and accidental trigger risks make naive integration unsafe and unreliable. vrspark needs a BCI integration spec that delivers useful focus/calm/kinesis signals to the HUD and control flows without coupling the HUD to vendor SDK details.

## Solution

Implement a Neurosity Crown producer/bridge that uses `@neurosity/sdk` outside Android WebView Bluetooth constraints, subscribes to processed metrics (`focus`, `calm`, `kinesis`, `signalQuality`, `status`), normalizes them into the Spec 001 contract, and publishes them with freshness/timestamp metadata. Kinesis commands are debounced, thresholded, and exposed as explicit events before any control action is taken.

## User Stories

1. As a BCI user, I want Crown focus updates in the HUD, so that I can monitor concentration during XR work.
2. As a BCI user, I want Crown calm updates in the HUD, so that I can monitor stress/flow state.
3. As a BCI user, I want signal quality visible per relevant channel/group, so that I can adjust the headset when data is unreliable.
4. As a BCI user, I want Crown battery and connection status visible, so that I can anticipate interruptions.
5. As an XR operator, I want BCI data marked stale/offline when disconnected, so that I do not act on old brain-state data.
6. As a power user, I want kinesis triggers to require confidence threshold and debounce, so that accidental activations are minimized.
7. As a power user, I want kinesis events shown before/when actions fire, so that I can understand why a hands-free action occurred.
8. As an XR developer, I want BCI integration to run as a bridge/service, so that WebView Web Bluetooth limitations do not block the HUD.
9. As an XR developer, I want only processed BCI metrics sent over mobile/XR links by default, so that bandwidth is conserved.
10. As an XR developer, I want raw EEG streaming to be an explicit opt-in debug mode, so that high-bandwidth data is never accidental.
11. As a security-conscious developer, I want no Neurosity credentials stored in repo files, so that secrets are not leaked.
12. As an XR developer, I want BCI producer output to conform to Spec 001, so that HUD rendering is plug-compatible.
13. As an XR developer, I want NTP timestamps attached to BCI messages, so that fusion can align them with video/biometrics.
14. As a QA tester, I want simulated BCI streams for tests, so that behavior can be validated without wearing the headset.
15. As a BCI user, I want kinesis control actions to be configurable and disableable, so that I remain in control.
16. As an XR operator, I want clear auth-required/offline states, so that I can recover the Crown connection.
17. As an XR developer, I want per-command kinesis mappings, so that different gestures can map to different UI actions.
18. As an XR developer, I want rate/timestamp anomaly handling, so that out-of-order or delayed packets do not corrupt HUD state.
19. As a product owner, I want BCI to enhance, not block, HUD operation, so that the system remains usable without Crown.
20. As a HUD user, I want focus-driven notification collapse to be explainable by visible focus score, so that UI changes feel predictable.

## Implementation Decisions

- **Primary seam alignment**: BCI bridge publishes normalized `bci` and `sources.crown` data/events compatible with Spec 001; HUD never talks directly to Neurosity SDK.
- **Runtime placement**: Node.js/Go bridge/service (headless worker) owns Neurosity SDK connection, auth state, subscriptions, and reconnect logic. Browser HUD receives normalized contract data only.
- **Default streams**: `focus`, `calm`, `kinesis`, `signalQuality`, `status`. Raw `brainwaves("raw")` disabled by default; if enabled for debug, it uses a separate dev-only channel and is not required by HUD.
- **Kinesis safety**: configurable `threshold` (default 0.85), `debounceMs` (default 1000), per-command enable/disable, and explicit action mapping. No destructive action fires without a visible event.
- **Freshness**: BCI producer emits `lastSeenAt`, status transitions, and staleness after timeout; HUD displays stale/offline distinctly.
- **Time sync**: BCI events include producer timestamp and NTP-aligned timestamp field used by fusion. Clock skew warnings are surfaced in producer health.
- **Secrets/config**: device ID/auth/config supplied through environment/runtime config outside git; repo contains only `.env.example` keys.
- **Reference standards**: nreal-ai repos offer no direct BCI implementation; use their boundary/documentation discipline only. Neurosity SDK usage follows source-documented stream model; implementation details stay behind the bridge boundary.

## Testing Decisions

- **What makes a good test**: tests exercise external producer behavior using simulated Neurosity event streams and assert normalized contract output, debounce/threshold behavior, staleness transitions, and auth/offline handling.
- **Modules to test**: stream normalization, kinesis threshold/debounce/action mapping, freshness/status state machine, schema conformance to Spec 001, and config/secret handling.
- **Prior art**: fixture-driven contract tests from Spec 001; event-driven subscriber tests similar to common Node service patterns.

## Out of Scope

- Clinical/medical EEG interpretation.
- Training motor imagery models inside vrspark.
- Raw EEG storage/analytics pipeline.
- Always-on cloud dependency when local bridge mode is possible.

## Further Notes

- Kinesis-triggered UI actions should start as non-destructive (pane focus, notification expand/collapse) before any code-apply or irreversible action.
