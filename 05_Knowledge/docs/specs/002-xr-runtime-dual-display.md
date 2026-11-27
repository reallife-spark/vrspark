# Spec 002: XR Runtime & Dual-Display Isolation

- Status: draft
- Owning repo: `reallife-spark/vrspark`
- Affected repos: `reallife-spark/vrspark`; possible future coordination with OpenCode Mobile/runtime repos if productized
- Source: `03_Sources/gemini/nreal-light-s24-xr-architecture-setup-guide.txt`
- Triage: `ready-for-agent` when published to GitHub Issues

## Problem Statement

Nreal Light on S24 Ultra spans conflicting display/runtime modes: Samsung DeX can capture the glasses interface and block Nebula; phone touchscreen and glasses display must be isolated for OpenCode Mobile + HUD workflows; AR/MR, productivity HUD, and VR/SBS modes have different tracking/runtime constraints. Without an explicit runtime-mode spec and runbook, setup is fragile, user-visible failures are confusing, and HUD prototypes cannot reliably target the right display plane.

## Solution

Define a runtime-mode and dual-display isolation spec with a reproducible runbook and a lightweight mode manager contract. The spec covers DeX/Nebula conflict resolution, DeX Extend dual-display isolation, audio/link-routing rules, and mode selection for AR/MR, Productivity HUD, and VR/SBS. The first implementation slice is documentation + validation checklist + a small runtime-state adapter that reports active mode/health into the Unified HUD State Contract.

## User Stories

1. As an XR operator, I want a clear mode-selection guide, so that I can choose AR/MR, DeX HUD, or VR/SBS for the task.
2. As an XR operator, I want DeX auto-start conflict steps documented, so that Nebula can initialize reliably with Nreal Light.
3. As an XR operator, I want Nebula permission/battery requirements documented, so that 6DoF AR Space tracking is not interrupted by OS restrictions.
4. As an XR operator, I want a warning not to lock the phone during Nebula AR Space, so that SLAM tracking is not suspended.
5. As an XR operator, I want a DeX Extend setup checklist, so that the glasses become Display 1 while the phone remains Display 0.
6. As an OpenCode Mobile user, I want OpenCode Mobile to remain on the S24 touchscreen, so that I can interact with code/agent controls directly.
7. As a HUD user, I want the custom multi-pane UI launched only in DeX on the glasses, so that the HUD is not mixed with phone interactions.
8. As an OpenCode Mobile user, I want link taps from OpenCode Mobile to avoid hijacking the DeX Chrome HUD, so that HUD layout remains stable.
9. As an XR operator, I want audio routing rules documented, so that temple speakers and dictation microphone behave predictably.
10. As an XR operator, I want a VR/SBS mode checklist, so that I can play 3D content or stream PC VR correctly.
11. As an XR operator, I want runtime preflight checks, so that I know before launch whether DeX/Nebula/permissions/cables are misconfigured.
12. As an XR developer, I want runtime mode reported in the HUD state contract, so that UI and logging can reflect actual mode.
13. As an XR developer, I want adapter status for DeX, Nebula, SBS, and audio routing, so that failures are diagnosable.
14. As an XR operator, I want power/thermal safeguards included, so that long sessions do not cause unstable USB/power behavior.
15. As an XR operator, I want optical HUD rules included in runtime docs, so that every HUD mode respects transparent-black constraints.
16. As an XR developer, I want mode-specific capability flags, so that features can be enabled/disabled by runtime.
17. As an XR operator, I want recovery steps for common failures, so that I can restore a session without guessing.
18. As a QA tester, I want acceptance checks per mode, so that device validation is repeatable.
19. As an XR developer, I want runtime assumptions labeled by Android/One UI/Nebula version, so that behavior changes are traceable.
20. As a product owner, I want the initial slice to be runbook + runtime-state reporting, so that we can stabilize operations before deeper automation.

## Implementation Decisions

- **Primary seam alignment**: runtime mode manager emits `session.mode`, `runtime.display`, and source health into the Spec 001 Unified HUD State Contract; it does not own HUD rendering.
- **Mode model**: canonical modes `arMr`, `dexHud`, `vrSbs`. Each mode has prerequisites, capabilities, tracking level, and known failure modes.
- **Conflict resolution**: Nebula/AR path requires DeX auto-start disabled and Nebula legacy v3.8.0/v3.8.1 assumptions documented; DeX HUD path requires Extend mode and auto touchpad takeover disabled.
- **Display isolation**: OpenCode Mobile is Display 0 (phone touchscreen) only; custom HUD PWA is Display 1 (DeX/glasses) only. No programmatic migration of OpenCode Mobile into DeX window.
- **Link/audio routing**: default browser split prevents OpenCode link hijack; audio routing is explicitly configured through Quick Settings/Separate app sound and reported as status.
- **Preflight checks**: checklist covers cable/PD splitter, DP Alt Mode, DeX settings, Nebula version/permissions, battery optimization, Game Booster USB PD pause, and light blocker state for SBS.
- **Runtime state adapter**: small module/service reports observed/selected mode, prerequisite checks, and warnings. Initial implementation may be manual/operator-confirmed inputs before automated Android introspection.
- **Documentation-first slice**: create runbook + validation matrix under `06_Projects/runtime-runbook/`; automation is incremental.
- **Reference standards**: nreal-ai WebXR samples inform browser/PWA runtime simplicity; NRSDK samples inform legacy Nreal Light runtime assumptions; neither is a direct runtime dependency.

## Testing Decisions

- **What makes a good test**: tests verify externally observable runtime outcomes and checklist completeness, not private OS internals. Where automation is unavailable, scripted manual validation with expected/actual evidence is acceptable.
- **Modules to test**: mode model validation, preflight checklist evaluator, runtime-state adapter output against Spec 001 schema, and runbook scenario coverage.
- **Prior art**: fixture-driven schema/adapter tests as in Spec 001; manual device validation checklists similar to hardware runbooks in repo docs.

## Out of Scope

- Building a custom Android launcher or replacing DeX/Nebula.
- Automated rooting/system-level display routing hacks.
- NRSDK 6DoF spatial app implementation beyond documented compatibility constraints.
- Full OpenCode Mobile product changes; only integration constraints are specified here.

## Further Notes

- This spec is a dependency for reliable HUD demos on physical Nreal Light hardware.
- Device behavior must be labeled with observed Android/One UI/S24 Ultra/Nebula versions when validated.
