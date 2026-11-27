# ADR 0001: Adopt Gemini XR architecture doc as initial vrspark source

- Status: accepted
- Date: 2026-11-27

## Context

The user requested a new full-spine repo `vrspark` and asked to base its main functions on a Gemini session. The accessible source was a Google Doc export provided via Drive.

## Decision

- Create `vrspark` as a full-spine repo under `/mnt/knowledge/vrspark` from `vault_templete`.
- Capture the Google Doc export as immutable provenance in `03_Sources/gemini/nreal-light-s24-xr-architecture-setup-guide.txt`.
- Distill the source into five main functions: XR hardware/runtime architecture; OS display/runtime mode management; dual-display productivity isolation; Neurosity Crown BCI integration; multimodal fusion with NVIDIA DeepStream.
- Register the repo in Home as `reallife-spark/vrspark`, `Spine: full`.

## Consequences

- vrspark has a traceable baseline for architecture and setup work.
- Future implementation details must cite or supersede this source via ADRs/docs.
- GitHub remote is configured locally; remote repo creation/push remains an explicit user action.
