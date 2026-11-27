# vrspark User Manual

## What this repo is

vrspark is the working repo for a real-time multimodal XR stack: Nreal Light glasses tethered to a Samsung Galaxy S24 Ultra, with optional Neurosity Crown, Galaxy Watch Ultra, and NVIDIA DeepStream fusion.

## Quick start

1. Read `00_System/CONTEXT.md` and `05_Knowledge/docs/main-functions.md`.
2. Review source provenance in `03_Sources/gemini/README.md`.
3. Use `02_Daily/PLAN.md` for active work.
4. Before implementation decisions, check device/runtime constraints in `docs/agents/domain.md`.

## Main workflows

- **Capture sources**: immutable external material in `03_Sources/`; distilled notes in `05_Knowledge/`.
- **Design decisions**: record significant choices in `docs/adr/`.
- **Prototypes**: place experiments under `06_Projects/` with device assumptions and validation notes.
- **Issue tracking**: GitHub Issues (default; confirm via `/setup-matt-pocock-skills`).

## Safety/constraints checklist

- Use charge-and-play splitter for long XR sessions; monitor thermals.
- Pure black `#000000` is transparent on Nreal Light optical displays.
- Avoid Android power-button lock during Nebula AR Space sessions.
- Request Android runtime camera permission before opening UVC device on Android 15/16+.
- Do not commit secrets, personal account data, or raw private health exports.
