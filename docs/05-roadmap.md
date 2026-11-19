# Driveline — Roadmap & Release Plan

| | |
|---|---|
| **Status** | Draft v0.1 |
| **Owner** | Product owner |
| **Last updated** | 2026-11-19 |

Two-week sprints. Dates are targets assuming roughly 15–20 hours of development a week; the plan is re-forecast at every sprint review using actual velocity. Scope is fixed per milestone; dates move before quality does.

## 1. Milestones

| Milestone | Version | Target | Exit criteria |
|---|---|---|---|
| **M0 Foundations** | — | Sprint 0 | Docs approved, ADRs accepted, repo + CI/CD + AWS accounts live, spike S-01 done |
| **M1 Private alpha** | v1.0 | End of Sprint 6 | All v1 "Must" requirements done; used daily by the owner for 2 weeks without data issues |
| **M2 Public launch** | v1.5 | End of Sprint 10 | v1.5 "Must" done; closed beta (≥ 20 testers) feedback addressed; store approval |
| **M3** | v2.0 | ~Sprint 14 | GPS trips, business mileage log, PDF reports |
| **M4** | v3.0 | When active users per metro area justify it | Crowdsourced prices behind a feature flag |

## 2. Sprint plan to public launch

### Sprint 0 — Foundations

- Approve charter, PRD, architecture; accept ADRs 0002–0008
- AWS Organizations: dev and prod accounts, IAM Identity Center, budgets, CloudTrail
- GitHub repo, branch protection, issue labels, project board, templates
- Monorepo skeleton: Expo app, `domain`, `importers`, `config`, `amplify/`
- CI pipeline (lint, type-check, test, audit, secret scan) green on an empty project
- **Spike S-01:** prototype sync (outbox push, cursor pull, conflict) between two simulators and an Amplify sandbox; report against NFR-004
- Trademark and store-name check for "Driveline" (Q3)

### Sprint 1 — Domain core

- Domain package: units, money, entry types, validation (FR-024)
- Fuel economy engine with all golden tests from the [spec](specs/fuel-economy.md)
- Local SQLite schema + migrations; repositories

### Sprint 2 — Vehicles and fill-ups

- Onboarding with units (FR-001, FR-004); guest mode
- Vehicles CRUD (FR-010, FR-011 fuel types, FR-012)
- Fill-up form with pre-fill and auto-calculation (FR-020 to FR-024, FR-026)
- Design system basics (buttons, inputs, lists, theming, dark mode)

### Sprint 3 — Other entries and dashboard

- Expenses, services, income (FR-030 to FR-033)
- Vehicle dashboard (FR-050)
- Entry history list with edit/delete

### Sprint 4 — Accounts and sync

- Cognito sign-up/sign-in with email, Apple, Google (FR-002, FR-006)
- Sync engine production version; guest → account migration (FR-003)
- Account deletion (FR-005)

### Sprint 5 — Reminders and reports

- Reminders with templates, local notifications, auto-reset (FR-040 to FR-042, FR-044)
- Server reminder Lambda + push (ADR-0006)
- Charts and filters (FR-051, FR-052)

### Sprint 6 — Portability and hardening → **v1.0 alpha**

- CSV export (FR-060); Fuelio and Drivvo import with preview and dedupe (FR-061 to FR-064)
- E2E suite for critical journeys; accessibility pass; performance pass (NFR-001)
- Runbooks; production deploy; TestFlight / Play internal release

### Sprints 7–10 — v1.5 public launch

- Subscriptions + entitlement webhook (FR-070, FR-075, FR-013)
- Receipt scanning (FR-071, FR-034)
- EV/PHEV charging and combined costs (FR-025, FR-053)
- Household sharing with server-enforced roles (FR-072)
- Predicted reminders (FR-043)
- Privacy policy, store listings, screenshots, closed beta, launch

## 3. Spikes (time-boxed investigations)

| ID | Question | Time-box | Sprint |
|---|---|---|---|
| S-01 | Can the custom sync meet NFR-004 on Amplify Gen 2? If not, evaluate a third-party sync engine | 4 days | 0 |
| S-02 | Fuelio and Drivvo export formats: collect samples, document field mapping | 1 day | 5 |
| S-03 | Textract AnalyzeExpense accuracy on 30 real fuel receipts | 2 days | 7 |
| S-04 | Background GPS reliability and battery impact on iOS and Android | 3 days | 11 |

## 4. Backlog management

- GitHub Issues + a GitHub Project board with columns: **Backlog → Ready → In progress → In review → Done**.
- Labels: `type:feature|bug|chore|spike|docs`, `area:app|domain|backend|sync|infra`, `priority:p0–p3`, `release:v1|v1.5|v2|v3`.
- Each issue links to its requirement ID; each PR links to its issue.

## 5. Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 2026-11-19 | Initial draft |
