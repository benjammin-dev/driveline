# ADR-0007: Subscriptions through RevenueCat

- **Status:** Proposed
- **Date:** 2026-11-19
- **Related:** FR-013, FR-070, FR-075

## Context

Digital subscriptions in iOS and Android apps must use Apple's and Google's in-app purchase systems. Validating receipts, handling renewals, refunds, grace periods and cross-platform entitlements is error-prone to build.

## Options considered

1. **Direct StoreKit 2 + Google Play Billing** with our own server validation — no vendor fee; significant work and edge cases.
2. **RevenueCat** — SDK for both stores, server-side validation, webhooks; free up to a revenue threshold, then a percentage fee.

## Decision

Use **RevenueCat** (`react-native-purchases`). A RevenueCat **webhook** calls a Lambda that writes the `Entitlement` record. **The server's `Entitlement` record is the only source of truth for Pro** in authorisation rules; the app's local entitlement is only used for UI.

## Consequences

- Fast, reliable subscriptions on both platforms with restore and cross-device support.
- Revenue share above RevenueCat's free tier; acceptable at our scale and revisited if revenue grows.
- Webhook endpoint must verify RevenueCat's authorisation header and be idempotent.
