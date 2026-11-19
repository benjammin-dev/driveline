# Driveline

**Log a fill-up in ten seconds, know your true cost per mile, and never miss a service.**

Driveline is an offline-first iOS and Android app for tracking fuel, charging, expenses and maintenance across your vehicles, with cloud sync on AWS.

> **Status:** Planning (Sprint 0). No application code yet: the documentation below is being reviewed and approved first.

## Documentation

| Document | Purpose |
|---|---|
| [Project Charter](docs/00-project-charter.md) | Vision, goals, scope, risks |
| [Product Requirements](docs/01-product-requirements.md) | Features, user stories, acceptance criteria |
| [Architecture](docs/02-architecture.md) | System design, data model, flows |
| [Engineering Standards](docs/03-engineering-standards.md) | Process, code, testing, CI/CD, review |
| [Security & Privacy](docs/04-security-privacy.md) | Threat model, data handling, AWS security |
| [Roadmap](docs/05-roadmap.md) | Milestones and sprint plan |
| [Architecture Decision Records](docs/adr/README.md) | Why key technical choices were made |
| [Fuel Economy Spec](docs/specs/fuel-economy.md) | Exact calculation rules and golden tests |

## Tech stack (proposed)

- **App:** Expo / React Native, TypeScript, Expo Router, SQLite (expo-sqlite + Drizzle)
- **Backend:** AWS Amplify Gen 2: Cognito, AppSync, DynamoDB, S3, Lambda (us-west-2)
- **Services:** Expo Push Service, RevenueCat, Amazon Textract
- **Tooling:** pnpm workspaces, Vitest, Jest, Maestro, GitHub Actions, EAS

## Planned repository layout

```
apps/mobile        Expo app
packages/domain    Pure business logic (economy math, units, validation)
packages/importers Fuelio / Drivvo importers
packages/config    Shared lint / TS / format configs
amplify/           Backend definition
docs/              Documentation
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). Security issues: see [SECURITY.md](SECURITY.md).

## Licence

To be decided before any public release. Until then, all rights reserved.
