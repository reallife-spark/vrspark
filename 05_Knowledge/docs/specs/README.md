# vrspark Specs

Specs distilled from `03_Sources/gemini/nreal-light-s24-xr-architecture-setup-guide.txt` and the vrspark main functions. These are local spec drafts until `reallife-spark/vrspark` exists on GitHub and issues can be published with `ready-for-agent`.

## Primary seam

**Unified HUD State Contract** — canonical normalized state published to `ui.hud.state` (10–15 Hz), consumed by the Nreal Light HUD. Producers (Nreal/S24 media, Neurosity Crown, Galaxy Watch Ultra, DeepStream inference) publish normalized telemetry; the fusion service aligns by NTP timestamp and emits the canonical HUD state. The HUD renders external behavior only against this contract.

## Specs

| Spec | Slice | Status | File |
|---|---|---|---|
| Unified HUD State Contract & HUD Prototype | HUD prototype / contract | draft | `001-unified-hud-state-contract.md` |
| XR Runtime & Dual-Display Isolation | DeX/Nebula/SBS runbook + mode manager | draft | `002-xr-runtime-dual-display.md` |
| Neurosity Crown BCI Integration | BCI producer + HUD controls | draft | `003-neurosity-bci-integration.md` |
| Multimodal Fusion Pipeline | NATS/MQTT + DeepStream + biometrics/EEG alignment | draft | `004-multimodal-fusion-pipeline.md` |

## Publishing

Target tracker: GitHub Issues in `reallife-spark/vrspark` per `docs/agents/issue-tracker.md`. Apply `ready-for-agent` when scope, owning repo, affected repos, and acceptance checks are explicit. Cross-repo coordination goes through Home issues.
