# Driveline — Project Charter

| | |
|---|---|
| **Status** | Draft v0.1 |
| **Owner** | Product owner (you) |
| **Last updated** | 2026-11-19 |
| **SDLC phase** | Planning |

## 1. Vision

Driveline is a mobile app that helps drivers understand what their vehicles really cost to run. It combines the speed and simplicity of offline fuel loggers with cloud sync, shared vehicles and smart automation, and it keeps logging free forever.

> **One-line pitch:** Log a fill-up in ten seconds, know your true cost per mile, and never miss a service.

## 2. Problem statement

Drivers who want to track fuel economy and running costs choose between:

- **Offline-first loggers** (e.g. Fuelio) that are fast and free but rely on manual backups and offer limited multi-device or household use.
- **Account-based trackers** (e.g. Drivvo) that sync across devices but lock useful features behind subscriptions.

Neither handles electric and hybrid vehicles as first-class citizens. Both still require tedious manual entry from paper receipts.

## 3. Goals and success measures

| Goal | Measure | Target (12 months after public launch) |
|---|---|---|
| Fast, reliable logging | Median time to log a fill-up | ≤ 10 seconds |
| Accurate numbers | Fuel-economy results match the reference algorithm test suite | 100% of test cases pass |
| Users stay | 90-day retention of users who log 3+ fill-ups | ≥ 40% |
| Sustainable to run | Monthly AWS cost per monthly active user | ≤ $0.03 |
| Sustainable business | Free → Pro conversion | ≥ 3% |
| Quality | Crash-free sessions | ≥ 99.5% |

## 4. Scope

### In scope (v1 to v3)

- iOS and Android apps built from one codebase
- Vehicles, fill-ups and EV charges, expenses, services, income
- Fuel-economy and cost reporting
- Maintenance reminders by date and distance
- Offline use with cloud sync after sign-in
- Import from Fuelio and Drivvo exports; CSV export
- Pro subscription: receipt scanning, household sharing, GPS trips, business mileage reports
- Crowdsourced fuel prices (v3)

### Out of scope (for now)

- Web dashboard (re-evaluate after v2)
- Fleet or business accounts with many drivers
- OBD-II dongle integration
- Navigation or route planning
- Markets outside North America at launch (units and currencies are designed to be international, but launch marketing is US and Canada)

## 5. Stakeholders

| Role | Who | Responsibility |
|---|---|---|
| Product owner | You | Vision, backlog priority, acceptance of features |
| Engineering | You, plus contributors and AI pair programmer | Design, build, test, operate |
| Beta testers | Friends, family, enthusiast forums | Feedback during TestFlight / Play internal testing |
| End users | Everyday drivers, rideshare drivers, households | Use the app |

## 6. Constraints and assumptions

- **Budget:** self-funded; AWS spend must stay within a monthly budget alert (initially $25/month).
- **Team:** one primary developer, so the process is lightweight Agile (see [Engineering Standards](03-engineering-standards.md)).
- **Platform:** AWS for the backend; Expo / React Native for the app.
- **Stores:** Apple App Store and Google Play rules govern subscriptions and privacy disclosures.
- **Assumption:** most users log 4 to 8 events a month per vehicle, so data volume per user is small.

## 7. Key risks

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Offline sync conflicts corrupt or lose data | Medium | High | Simple sync design with per-record versions; extensive tests (ADR-0004) |
| Fuel-economy math disagrees with users' expectations | Medium | High | Published algorithm spec and golden test cases ([spec](specs/fuel-economy.md)) |
| Name "Driveline" conflicts with an existing trademark or app | Medium | Medium | Trademark and store search before public launch |
| AWS service changes or deprecations | Medium | Medium | Prefer stable core services; isolate vendors behind interfaces (example: Amazon Pinpoint ended support on 2026-10-30) |
| Crowdsourced prices never reach useful density | High | Low | Shipped last, behind a feature flag |
| Store rejection (privacy, subscriptions) | Low | Medium | Follow store guidelines checklist before each submission |

## 8. Milestones

See the [Roadmap](05-roadmap.md) for detail.

| Milestone | Content |
|---|---|
| M0 Foundations | Docs approved, repo, CI/CD, AWS environments |
| M1 Private alpha (v1) | Core logging, reports, reminders, offline + sync, import/export |
| M2 Public launch (v1.5) | Receipt scanning, EV charging, household sharing, Pro subscription |
| M3 v2 | GPS trips, business mileage log |
| M4 v3 | Crowdsourced fuel prices |

## 9. Document map

| # | Document | Purpose |
|---|---|---|
| 00 | Project Charter (this file) | Why, what, who |
| 01 | [Product Requirements](01-product-requirements.md) | Features, user stories, acceptance criteria |
| 02 | [Architecture](02-architecture.md) | How the system is built |
| 03 | [Engineering Standards](03-engineering-standards.md) | How we write, test, review and ship code |
| 04 | [Security & Privacy](04-security-privacy.md) | Threats, data handling, compliance |
| 05 | [Roadmap](05-roadmap.md) | Releases and sprint plan |
| ADR | [Architecture Decision Records](adr/README.md) | Why each key technical choice was made |
| Spec | [Fuel Economy Algorithm](specs/fuel-economy.md) | Exact calculation rules |

## 10. Approval

| Role | Name | Decision | Date |
|---|---|---|---|
| Product owner | | ☐ Approved | |
