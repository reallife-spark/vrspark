# vrspark

Full-spine repo for real-time multimodal XR architecture and prototyping around **Nreal Light (XREAL Light)** + **Samsung Galaxy S24 Ultra**.

## Main functions

1. **XR hardware/runtime architecture** — composite USB-C interfaces, power/thermal/optical constraints.
2. **OS display/runtime mode management** — DeX vs Nebula conflict resolution; AR/MR, HUD, VR/SBS modes.
3. **Dual-display productivity isolation** — OpenCode Mobile on phone, custom multi-pane HUD/PWA in DeX.
4. **BCI integration** — Neurosity Crown via `@neurosity/sdk` (focus/calm/kinesis/signal quality).
5. **Multimodal fusion** — Nreal video/audio + Watch biometrics + Crown EEG synchronized through broker + NVIDIA DeepStream into a unified HUD state.

## Start here

- `00_System/CONTEXT.md`
- `05_Knowledge/docs/main-functions.md`
- `03_Sources/gemini/README.md`

## Provenance

Primary design source captured from a user-provided Google Doc export on 2026-11-27: `03_Sources/gemini/nreal-light-s24-xr-architecture-setup-guide.txt`.
