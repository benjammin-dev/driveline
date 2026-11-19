# CLAUDE.md

Guidance for Claude Code when working in this repository.

## Project

Driveline is an offline-first iOS/Android app for tracking fuel, charging, expenses and maintenance, with an AWS backend. **Current phase: Sprint 0 (foundations).** Application code does not exist yet; follow the [Roadmap](docs/05-roadmap.md).

## Read before working

| Need | Document |
|---|---|
| What to build | [docs/01-product-requirements.md](docs/01-product-requirements.md) (requirement IDs `FR-xxx` / `NFR-xxx`) |
| How it's built | [docs/02-architecture.md](docs/02-architecture.md) and [docs/adr/](docs/adr/README.md) |
| How we work | [docs/03-engineering-standards.md](docs/03-engineering-standards.md) |
| Economy math | [docs/specs/fuel-economy.md](docs/specs/fuel-economy.md) (golden tests are normative) |
| Security rules | [docs/04-security-privacy.md](docs/04-security-privacy.md) |

## Stack

Expo / React Native + TypeScript (Expo Router, expo-sqlite + Drizzle) · AWS Amplify Gen 2 (Cognito, AppSync, DynamoDB, S3, Lambda) in us-west-2 · pnpm workspaces monorepo · Vitest, Jest + RNTL, Maestro · GitHub Actions, EAS.

Do **not** use Amplify Gen 1, Amplify DataStore or Amazon Pinpoint (see ADR-0004, ADR-0006).

## Commands

To be filled in during Sprint 0 once the monorepo exists (install, dev, test, lint, type-check, backend sandbox).

## Rules

- **Work from an issue** and reference its requirement IDs. If a request has no acceptance criteria, propose them before coding.
- **Branches:** `feat/<issue>-name`, `fix/…`, `docs/…`, `chore/…`, `spike/…` off `main`. Never commit directly to `main`; open a PR using `.github/pull_request_template.md`.
- **Commits:** Conventional Commits (`feat(scope): summary`).
- **Business logic** belongs in `packages/domain` as pure TypeScript: no I/O, no React, no AWS SDK.
- **Money** is integer minor units + ISO 4217 code. Never floating point.
- **Units:** store litres, kWh and odometer units; convert only for display.
- **IDs** are client-generated UUIDv7. Timestamps in UTC plus the user's IANA time zone.
- **Authorisation** is enforced server-side (AppSync rules / Lambda), never only in the UI.
- **Tests:** write tests with the code. `packages/*` coverage ≥ 90%. The fuel-economy golden tests must pass exactly.
- **Strings** go through `t('key')`; interactive elements need accessibility labels and roles.
- **Security:** no secrets in the repo; no personal data, tokens or receipt content in logs.
- **Dependencies:** justify new ones in the PR. Native modules or new vendors need an ADR.
- **Docs:** update the PRD, architecture, spec or ADRs in the same PR when behaviour or design changes, and add a `CHANGELOG.md` entry for user-visible changes.
- Check work against the Definition of Done in the engineering standards before calling a task finished.
