# nreal-ai Reference Standards

Reviewed 2026-11-27. These repos are coding/reference standards for vrspark, not implementation targets to copy wholesale. Preserve upstream licenses/attribution when borrowing patterns.

| Repo | Local path | HEAD | License | Use as reference for | Avoid/caution |
|---|---|---|---|---|---|
| `nreal-ai/uvc_android` | `/mnt/nvme/github/uvc_android` | `0e91d26` | none found | Nreal Light UVC/native capture constraints, Android NDK/CMake layout, frame metadata handling | Prototype quality; hardcoded local paths; not a style baseline |
| `nreal-ai/MixedRealityToolkit-Unity` | `/mnt/nvme/github/MixedRealityToolkit-Unity` | `bb34a67` | BSD-3-Clause | MRTK governance/process, large Unity project structure | Large fork; legacy/heavy for vrspark prototypes |
| `nreal-ai/NRSDK-MRTK-Samples` | `/mnt/nvme/github/NRSDK-MRTK-Samples` | `08c8ea1` | MIT | Nreal+MRTK interaction patterns, `.editorconfig` C# conventions, sample scene organization | Legacy stack: Unity 2020.3.x, MRTK 2.8, NRSDK 1.9.3 |
| `nreal-ai/webxr-polyfill` | `/mnt/nvme/github/webxr-polyfill` | `8cfc8bc` | Apache-2.0 | JS library packaging, compatibility/fallback boundaries, caveat documentation, test/build scripts | Older JS toolchain; not a modern frontend baseline |
| `nreal-ai/webxr-samples` | `/mnt/nvme/github/webxr-samples` | `90af42a` | MIT | Simple WebXR session/input/render structure for HUD/PWA experiments | No package/test baseline; samples only |
| `nreal-ai/NRSDK-XR-Plugin` | `/mnt/nvme/github/NRSDK-XR-Plugin` | `8341cc0` | MIT | Small Unity XR plugin package layout, runtime/editor split, asmdef boundaries, focused loader/settings design | NRSDK/XR plugin APIs are legacy; use structure, not runtime dependency |
| `nreal-ai/webvr-polyfill` | `/mnt/nvme/github/webvr-polyfill` | `c085444` | Apache-2.0 | Historical compatibility-layer patterns and caveat docs | Legacy WebVR; do not target for new implementation |

## Adoption rules

- Prefer **boundary-first** modules: adapters/producers/fusion/HUD separated by contract.
- Make assumptions explicit: OS/One UI/Nebula/NRSDK/Android version and hardware constraints.
- Document unsupported behavior/caveats in README/specs.
- Use nreal-ai repos for Nreal Light device behavior and XR/WebXR structure; use vrspark specs for current architecture decisions.
