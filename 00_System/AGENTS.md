# vrspark

vrspark is a full-spine repo for real-time multimodal XR architecture and prototyping around Nreal Light (XREAL Light) and Samsung Galaxy S24 Ultra.

## Start here

- `00_System/CONTEXT.md`
- `00_System/MISSION.md`
- `00_System/INTENT.md`
- `05_Knowledge/MEMORY.md`
- `02_Daily/PLAN.md`
- `05_Knowledge/docs/main-functions.md`

## Main functions (distilled)

1. **XR hardware/runtime architecture** — model Nreal Light composite USB-C interfaces, S24 Ultra DP Alt Mode/GPU role, power/thermal/optical constraints.
2. **OS display/runtime mode management** — resolve Samsung DeX vs Nebula conflicts; define AR/MR, productivity HUD, and VR/SBS modes.
3. **Dual-display productivity isolation** — keep OpenCode Mobile on S24 touchscreen while rendering custom multi-pane PWA/HUD in DeX on glasses.
4. **BCI integration** — ingest Neurosity Crown metrics (`focus`, `calm`, `kinesis`, `signalQuality`, status/battery) via `@neurosity/sdk` over Wi-Fi/WebSockets.
5. **Multimodal fusion pipeline** — synchronize Nreal UVC/audio, Galaxy Watch Ultra biometrics, and Crown EEG through broker + NVIDIA DeepStream metadata fusion into a single HUD state stream.

## Source

- Immutable capture: `03_Sources/gemini/nreal-light-s24-xr-architecture-setup-guide.txt`
- Capture metadata: `03_Sources/gemini/README.md`

## Agent skills

### Issue tracker

GitHub Issues in `reallife-spark/vrspark` (public). See `docs/agents/issue-tracker.md`.

### Triage labels

Default triage vocabulary (`needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`). See `docs/agents/triage-labels.md`.

### Domain docs

Single-context: root `GLOSSARY.md` + `docs/adr/`. See `docs/agents/domain.md`.
