# ADR-0003: Monorepo with a pure shared domain package

- **Status:** Proposed
- **Date:** 2026-11-19
- **Related:** FR-026, FR-050, FR-061, NFR-010

## Context

The same business rules (economy math, unit conversion, validation, import parsing) must run on the device and in Lambda functions. Duplicated logic would drift and produce different numbers in different places.

## Options considered

1. **Separate repos** for app and backend, logic copied or published as a package — versioning overhead.
2. **Monorepo with pnpm workspaces** — app, backend and packages in one repo, shared code imported directly.

## Decision

Use a **pnpm workspaces monorepo**. Business rules live in `packages/domain`: pure TypeScript functions with **no I/O, no React, no AWS SDK**. The app and Lambdas both depend on it. Importers live in `packages/importers`.

## Consequences

- One pull request can change a rule, its tests, the app and the backend together.
- The domain package is fast and easy to test; it carries the golden tests from the [fuel economy spec](../specs/fuel-economy.md) and targets ≥ 90% coverage.
- CI must run only what changed to stay fast (path filters or Turborepo caching, decided during Sprint 0).
