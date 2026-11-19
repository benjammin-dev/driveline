# Driveline — Product Requirements Document (PRD)

| | |
|---|---|
| **Status** | Draft v0.1 |
| **Owner** | Product owner |
| **Last updated** | 2026-11-19 |
| **Related** | [Charter](00-project-charter.md) · [Architecture](02-architecture.md) · [Fuel economy spec](specs/fuel-economy.md) |

This document is the source of truth for **what** Driveline does. Each requirement has a stable ID (`FR-xxx`, `NFR-xxx`) so it can be referenced from issues, pull requests and tests. Once a requirement ships, its ID is never reused.

**Priority** uses MoSCoW (Must / Should / Could / Won't for this release). **Release** is the first version the requirement ships in.

---

## 1. Personas

| Persona | Description | Primary needs |
|---|---|---|
| **Maya, the budgeter** | Commutes daily, one car, wants to know what driving costs her each month | Fast fill-up logging, monthly cost, reminders |
| **Sam, the enthusiast** | Two cars and a motorcycle, tracks every mpg and service | Detailed stats, accurate economy, full history import from Fuelio |
| **The Okafor household** | Two adults sharing two cars | Both can log; shared totals; one place for service history |
| **Luis, the gig driver** | Drives for rideshare and delivery | Income vs expense, business mileage log for taxes |
| **Priya, the EV owner** | Plug-in hybrid, charges at home and in public | kWh and fuel in one place, true cost per mile |

## 2. Glossary

| Term | Meaning |
|---|---|
| **Entry** | Any logged event: fill-up, charge, expense, service or income |
| **Fill-up** | A liquid-fuel refuelling event (gasoline, diesel, LPG, etc.) |
| **Charge** | An electric charging event measured in kWh |
| **Full tank** | Fill-up where the tank was filled to the top; required to calculate economy |
| **Missed entry** | A flag saying one or more fill-ups were not logged before this one |
| **Economy** | Distance per volume (mpg, km/L) or volume per distance (L/100 km); kWh/100 mi for electric |
| **Guest mode** | Using the app with no account; data stays on the device |

---

## 3. Functional requirements

### Epic A — Onboarding and accounts

| ID | User story | Priority | Release |
|---|---|---|---|
| FR-001 | As a new user, I can start logging without creating an account (guest mode). | Must | v1 |
| FR-002 | As a user, I can create an account with email, Sign in with Apple, or Google. | Must | v1 |
| FR-003 | As a guest, when I create an account my existing on-device data is uploaded to my account without loss. | Must | v1 |
| FR-004 | As a user, I can choose my units (distance, volume, economy format, currency) at onboarding and change them later. | Must | v1 |
| FR-005 | As a user, I can delete my account and all my cloud data from inside the app. | Must | v1 |
| FR-006 | As a user, I can sign in on a second device and see the same data. | Must | v1 |

**Acceptance criteria**

- **FR-003:** *Given* a guest with 50 entries across 2 vehicles, *when* they create an account, *then* all 50 entries and both vehicles are present on the server and on a second signed-in device, and no duplicates exist.
- **FR-005:** *Given* a signed-in user, *when* they confirm account deletion, *then* their identity, entries, receipts and vehicles are deleted from all cloud storage within 30 days (immediately from active tables), and they receive confirmation. Required by Apple App Store guidelines.

### Epic B — Vehicles

| ID | User story | Priority | Release |
|---|---|---|---|
| FR-010 | As a user, I can add a vehicle with name, make, model, year, fuel type(s), tank capacity (optional) and starting odometer. | Must | v1 |
| FR-011 | As a user, I can set a vehicle's fuel configuration: single fuel, bi-fuel (e.g. gasoline + LPG), plug-in hybrid, or battery electric. | Must | v1 (fuel types), v1.5 (electric) |
| FR-012 | As a user, I can archive a vehicle I sold without losing its history. | Should | v1 |
| FR-013 | As a free user, I can have up to 3 active vehicles; Pro users have unlimited. | Must | v1.5 |
| FR-014 | As a user, I can add a photo to a vehicle. | Could | v1.5 |

### Epic C — Logging fill-ups and charges

| ID | User story | Priority | Release |
|---|---|---|---|
| FR-020 | As a user, I can log a fill-up with date/time, odometer, volume, price per unit or total cost, fuel type, full-tank flag, missed-entry flag, station (optional), and note. | Must | v1 |
| FR-021 | As a user, entering any two of volume, price per unit and total cost calculates the third. | Must | v1 |
| FR-022 | As a user, the form pre-fills the date, vehicle, fuel type and last station so a typical fill-up needs only odometer and two numbers. | Must | v1 |
| FR-023 | As a user, I can enter either the odometer reading or the trip distance since the last fill-up. | Should | v1 |
| FR-024 | As a user, I am warned if the odometer is lower than a previous entry or the volume exceeds tank capacity. | Must | v1 |
| FR-025 | As an EV/PHEV owner, I can log a charge with kWh added, cost, location type (home / public / work) and optional start/end battery %. | Must | v1.5 |
| FR-026 | As a user, I can edit or delete any entry, and economy figures recalculate. | Must | v1 |

**Acceptance criteria**

- **FR-020/FR-022:** *Given* a user with at least one previous fill-up, *when* they open "Add fill-up", *then* vehicle, fuel type, date (now) and station are pre-filled, and the entry can be saved with three fields typed.
- **FR-024:** *Given* a previous entry at odometer 45,210, *when* a user enters 45,100, *then* a warning explains the conflict and offers to correct it; saving is still allowed after confirmation (out-of-order entries are legitimate).
- **FR-026:** Economy is recalculated per the [fuel economy spec](specs/fuel-economy.md) for every affected interval.

### Epic D — Expenses, services and income

| ID | User story | Priority | Release |
|---|---|---|---|
| FR-030 | As a user, I can log an expense with category (insurance, parking, tolls, registration, washing, fines, other), amount, date, odometer (optional) and note. | Must | v1 |
| FR-031 | As a user, I can log a service with one or more service types (oil change, tires, brakes, etc.), parts and labour cost, shop, odometer and note. | Must | v1 |
| FR-032 | As a user, I can create my own expense categories and service types. | Should | v1 |
| FR-033 | As a gig driver, I can log income (amount, source, date) per vehicle. | Should | v1 |
| FR-034 | As a user, I can attach photos (receipts, invoices) to any entry. | Should | v1.5 |

### Epic E — Reminders

| ID | User story | Priority | Release |
|---|---|---|---|
| FR-040 | As a user, I can create a reminder by date, by distance, or both ("whichever comes first"), one-off or repeating. | Must | v1 |
| FR-041 | As a user, logging a matching service resets a repeating reminder automatically. | Must | v1 |
| FR-042 | As a user, I receive a push notification when a reminder is due, and an in-app badge. | Must | v1 |
| FR-043 | As a user, distance-based reminders show a predicted due date based on my recent driving rate. | Should | v1.5 |
| FR-044 | As a user, I get templates for common reminders (oil, tire rotation, registration, inspection). | Should | v1 |

**Acceptance criteria**

- **FR-043:** *Given* a vehicle averaging 1,000 mi/month over the last 90 days and an oil reminder due at +2,500 mi, *then* the reminder shows "about 2½ months" and notifies 14 days or 500 mi before due, whichever comes first (configurable).

### Epic F — Reports and statistics

| ID | User story | Priority | Release |
|---|---|---|---|
| FR-050 | As a user, I see a dashboard per vehicle: last economy, average economy, cost this month, cost per distance, next reminder. | Must | v1 |
| FR-051 | As a user, I can see charts: economy over time, monthly cost by category, fuel price over time. | Must | v1 |
| FR-052 | As a user, I can filter reports by vehicle and date range. | Must | v1 |
| FR-053 | As a PHEV owner, I see combined and per-energy cost per distance. | Should | v1.5 |
| FR-054 | As a Pro user, I can export a PDF report for a date range. | Could | v2 |

### Epic G — Data portability

| ID | User story | Priority | Release |
|---|---|---|---|
| FR-060 | As a user, I can export all my data as CSV (one file per entry type, zipped). | Must | v1 |
| FR-061 | As a switching user, I can import a Fuelio CSV backup. | Must | v1 |
| FR-062 | As a switching user, I can import a Drivvo CSV export. | Should | v1 |
| FR-063 | As a user, an import shows a preview and a summary (records found, skipped, warnings) before committing. | Must | v1 |
| FR-064 | As a user, re-importing the same file does not create duplicates. | Must | v1 |

### Epic H — Pro features

| ID | User story | Priority | Release |
|---|---|---|---|
| FR-070 | As a user, I can subscribe to Pro monthly or yearly through the App Store / Google Play, and restore purchases. | Must | v1.5 |
| FR-071 | As a Pro user, I can photograph a receipt and have date, volume, unit price, total and station extracted into the form for review. | Must | v1.5 |
| FR-072 | As a Pro user, I can share a vehicle with another account as **editor** or **viewer**, and revoke access. | Must | v1.5 |
| FR-073 | As a Pro user, I can record GPS trips (start/stop, auto-detected optional) with distance and route. | Should | v2 |
| FR-074 | As a Pro user, I can tag trips as business or personal and export a mileage log suitable for tax records. | Should | v2 |
| FR-075 | If a Pro subscription lapses, no data is lost; Pro-only creation features become read-only. | Must | v1.5 |

**Acceptance criteria**

- **FR-071:** Extracted values are never saved without the user reviewing the form. Target: correct extraction of total and volume on ≥ 85% of a 100-receipt test set.
- **FR-072:** A viewer cannot create, edit or delete entries for the shared vehicle (enforced on the server, not only in the UI).

### Epic I — Crowdsourced fuel prices (v3)

| ID | User story | Priority | Release |
|---|---|---|---|
| FR-080 | As a user, I can see nearby stations and recent reported prices on a map or list. | Should | v3 |
| FR-081 | As a user, I can opt in to share the price from my fill-ups anonymously. | Should | v3 |
| FR-082 | Prices older than 72 hours are shown as stale; obviously wrong reports are filtered. | Should | v3 |

---

## 4. Non-functional requirements

| ID | Category | Requirement |
|---|---|---|
| NFR-001 | Performance | App cold start to usable dashboard ≤ 2 s on a mid-range phone (3 years old). |
| NFR-002 | Performance | Saving an entry completes locally in ≤ 100 ms regardless of network. |
| NFR-003 | Offline | Every v1 feature except sign-in, import from cloud and Pro scanning works with no network. |
| NFR-004 | Sync | Changes made offline sync within 30 s of connectivity returning; no silent data loss under concurrent edits (see ADR-0004). |
| NFR-005 | Availability | Backend API availability ≥ 99.9% monthly. |
| NFR-006 | Security | All data encrypted in transit (TLS 1.2+) and at rest; see [Security & Privacy](04-security-privacy.md). |
| NFR-007 | Privacy | Location is never collected without explicit opt-in; GPS trips and price sharing are separate opt-ins. |
| NFR-008 | Accessibility | Meets WCAG 2.2 AA where applicable to mobile: dynamic type, screen readers (VoiceOver/TalkBack), 4.5:1 contrast, 44×44 pt touch targets. |
| NFR-009 | Internationalisation | All strings externalised; metric and imperial units; any ISO 4217 currency; dates and numbers localised. English at launch. |
| NFR-010 | Quality | Crash-free sessions ≥ 99.5%; domain logic unit test coverage ≥ 90%. |
| NFR-011 | Cost | Backend cost ≤ $0.03 per monthly active user at 10k MAU. |
| NFR-012 | Maintainability | Code meets [Engineering Standards](03-engineering-standards.md); every architecturally significant change has an ADR. |
| NFR-013 | Platform support | iOS and Android versions supported by the current Expo SDK at time of release. |
| NFR-014 | Data retention | Users can export all data at any time (FR-060); deleted accounts are purged per FR-005. |

## 5. Data definitions (summary)

Full schema lives in the [Architecture](02-architecture.md#5-data-model) document.

| Field | Rule |
|---|---|
| Odometer | Stored in the vehicle's native distance unit as a decimal, ≥ 0 |
| Volume | Stored in litres internally; displayed in user units |
| Energy | Stored in kWh |
| Money | Stored as integer minor units (cents) + ISO 4217 currency code; never floating point |
| Timestamps | Stored in UTC (ISO 8601) plus the user's IANA time zone at time of entry |
| IDs | Client-generated UUIDv7 so entries can be created offline |

## 6. Open questions

| # | Question | Owner | Due |
|---|---|---|---|
| Q1 | Final Pro pricing (proposal: $2.99/month, $19.99/year) | Product owner | Before v1.5 |
| Q2 | Free vehicle limit: 2 or 3? (proposal: 3) | Product owner | Before v1.5 |
| Q3 | Is "Driveline" clear in App Store, Google Play and USPTO? | Product owner | Before public beta |
| Q4 | Which receipt formats to prioritise for the scanning test set (US chains first?) | Engineering | Sprint planning v1.5 |

## 7. Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 2026-11-19 | Initial draft |
