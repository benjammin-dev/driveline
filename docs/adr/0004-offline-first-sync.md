# ADR-0004: Offline-first with SQLite and a custom sync engine

- **Status:** Proposed
- **Date:** 2026-11-19
- **Related:** FR-001, FR-003, FR-006, NFR-002, NFR-003, NFR-004

## Context

Users log fill-ups at gas stations, often with poor signal. Saving must be instant and never depend on the network. Users also want their data on several devices and, with Pro, shared with household members. Guest users have no account at all.

Amplify's built-in offline library, DataStore, belongs to Amplify Gen 1. Gen 1 is in maintenance mode and reaches end of life on 1 May 2027, so a new project should not depend on it.

## Options considered

1. **Online-only API calls** — simplest; fails NFR-002/003.
2. **Amplify DataStore (Gen 1)** — offline sync out of the box; tied to an end-of-life generation.
3. **Third-party sync engine** (e.g. PowerSync, ElectricSQL, WatermelonDB sync) — mature sync; adds a vendor and often a Postgres backend, departing from the chosen AWS stack.
4. **Local SQLite + small custom sync protocol over AppSync** — full control, fits our simple data shape; we own the complexity.

## Decision

Use **SQLite on the device (`expo-sqlite` + Drizzle ORM) as the local source of truth**, with a **custom sync engine**:

- Client-generated UUIDv7 IDs; every write goes into the local table and an **outbox** in one transaction.
- **Push:** outbox mutations are sent in order with the record `version`; the server applies optimistic concurrency.
- **Pull:** per sync scope (the user's own data and each shared vehicle), fetch records with `updatedAt > cursor`.
- **Conflicts:** last-writer-wins per record; the losing copy is kept locally as a conflict snapshot for 30 days and can be restored from the entry's history.
- **Deletes:** tombstones, purged after 90 days.
- **Guest → account:** on sign-up, the whole local database is enqueued for push.

Option 3 stays the fallback if spike **S-01** shows the custom engine cannot meet NFR-004 within two sprints.

## Consequences

- Instant saves and full offline use.
- We own a critical piece of code; it needs heavy testing: unit tests, property-based tests (random interleavings of offline edits) and an end-to-end test with two simulated devices.
- Schema migrations must be handled on device and server together; every migration is versioned and tested on a copy of real-shaped data.
