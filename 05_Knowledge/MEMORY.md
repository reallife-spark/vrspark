# Memory

## Standing state

- `vrspark` is a full-spine repo at `/mnt/knowledge/vrspark`, remote `https://github.com/reallife-spark/vrspark.git`, intended for Nreal Light + Samsung Galaxy S24 Ultra real-time multimodal XR architecture and prototyping.
- Primary source captured: `03_Sources/gemini/nreal-light-s24-xr-architecture-setup-guide.txt` (Google Doc export, 2026-11-27). Provenance in `03_Sources/gemini/README.md`.
- Main functions distilled from source: XR hardware/runtime architecture; DeX/Nebula/runtime mode management; dual-display OpenCode Mobile + DeX HUD isolation; Neurosity Crown BCI integration via `@neurosity/sdk`; multimodal fusion via broker + NVIDIA DeepStream metadata injection.
- nreal-ai reference repos reviewed 2026-11-27: uvc_android, MixedRealityToolkit-Unity, NRSDK-MRTK-Samples, webxr-polyfill, webxr-samples, NRSDK-XR-Plugin, webvr-polyfill; missing two cloned to `/mnt/nvme/github`. Standards captured in `05_Knowledge/docs/nreal-ai-reference-standards.md`.
- Draft specs created in `05_Knowledge/docs/specs/`: 001 Unified HUD State Contract/HUD prototype (primary seam `ui.hud.state`), 002 XR runtime/dual-display isolation, 003 Neurosity Crown BCI integration, 004 multimodal fusion pipeline.
- Pending: run `/setup-matt-pocock-skills` for tracker/triage/domain choices; create/publish GitHub repo and issues; pick first implementation slice (recommended Spec 001 mock HUD prototype).
