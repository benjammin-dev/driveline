# ADR-0006: Local notifications plus Expo Push Service

- **Status:** Proposed
- **Date:** 2026-11-19
- **Related:** FR-040 to FR-044

## Context

Reminders must notify users when due. Date-based reminders can be computed on the device; distance-based predictions and reminders on shared vehicles need the server.

Amazon Pinpoint, historically used by Amplify for push, reached end of support on 30 October 2026. Its raw push channel continues under AWS End User Messaging, while Amplify's Pinpoint-backed push features stopped working after that date.

## Options considered

1. **Local notifications only** — works offline; cannot react to data changed on another device or by a household member.
2. **AWS End User Messaging push** — AWS-native; we manage APNs/FCM credentials and device endpoints ourselves.
3. **Expo Push Service** — one API for APNs and FCM, integrates with `expo-notifications`; an additional (free at our scale) vendor.
4. **Third-party engagement platform** (OneSignal etc.) — marketing features we don't need.

## Decision

- **Date-based reminders:** scheduled locally with `expo-notifications` (works offline).
- **Server-triggered reminders:** a daily Lambda sends via the **Expo Push Service**, called through a `PushSender` interface so AWS End User Messaging can replace it without app changes.

## Consequences

- Reminders work offline for the common case.
- One extra vendor, isolated behind an interface.
- Device tokens are stored per user in the `Device` table and removed when Expo reports them invalid.
