# ADR-0001: Record architecture decisions

- **Status:** Accepted
- **Date:** 2026-11-19
- **Deciders:** Product owner, Engineering

## Context

Driveline will be built largely by one developer with AI assistance and, later, contributors. Without a record, the reasons behind choices get lost and are re-debated or accidentally undone.

## Decision

We record every architecturally significant decision as an ADR in `docs/adr/`, numbered sequentially, using [`template.md`](template.md). ADRs are reviewed in pull requests like code.

## Consequences

- Decisions are discoverable and reviewable in the same place as the code.
- Small overhead per decision (typically 15–30 minutes).
- Pull requests that make a significant change without an ADR should be sent back in review.
