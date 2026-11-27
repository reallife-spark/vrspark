# Plan

current: Draft vrspark specs from Gemini source and nreal-ai reference review.

## Now

- [x] Create `/mnt/knowledge/vrspark` from `vault_templete` (full spine).
- [x] Capture Gemini source with provenance in `03_Sources/gemini/`.
- [x] Distill main functions into steering and `05_Knowledge/docs/main-functions.md`.
- [x] Review nreal-ai repos and record coding/reference standards.
- [x] Draft local specs in `05_Knowledge/docs/specs/` (HUD contract, runtime/dual-display, BCI, multimodal fusion).
- [ ] Run `/setup-matt-pocock-skills` in vrspark and resolve issue tracker / triage / domain-doc choices.
- [ ] Create/publish `reallife-spark/vrspark` on GitHub and publish specs as issues with `ready-for-agent` when scope is accepted.
- [ ] Pick first implementation slice (recommended: Spec 001 HUD prototype against mock `ui.hud.state`).

## Later

- Add ADRs for transport choice (NATS vs MQTT), schema versioning, and DeepStream deployment boundary.
- Add prototype code under `06_Projects/` with device-specific validation checklists.
