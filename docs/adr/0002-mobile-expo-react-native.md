# ADR-0002: Build the app with Expo and React Native

- **Status:** Proposed
- **Date:** 2026-11-19
- **Related:** NFR-001, NFR-003, NFR-013, FR-073

## Context

Driveline needs iOS and Android apps from a single small team. Some features need native capabilities: camera (receipts), background location (GPS trips, v2), local notifications, secure storage, SQLite and in-app purchases.

## Options considered

1. **Native (Swift + Kotlin)** — best platform fit; two codebases, roughly double the effort.
2. **Flutter** — one codebase, strong performance; Dart only, smaller AWS/Amplify ecosystem for our use.
3. **React Native with Expo** — one TypeScript codebase shared with the backend and domain logic; Expo provides managed native modules (camera, notifications, SQLite, location, secure store), cloud builds (EAS Build), store submission (EAS Submit) and over-the-air JS updates (EAS Update). Expo SDK 58 (October 2026) targets React Native 0.88 and iOS 27.
4. **Web app / PWA** — fastest to start; weak background location, notifications and store presence.

## Decision

Use **Expo (SDK 58 at project start) with React Native and TypeScript**, Expo Router for navigation, and EAS for builds, submission and updates. Use development builds (not Expo Go) from day one because we depend on native modules.

## Consequences

- One language (TypeScript) across app, domain and backend.
- Fast iteration and over-the-air fixes for JS-only changes (within store rules).
- We must follow Expo's SDK cadence and upgrade regularly (budget one upgrade sprint-task per SDK release).
- Background GPS (v2) needs careful testing on both platforms; validated by a spike before v2.
