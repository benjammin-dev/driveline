# Driveline — Security & Privacy Plan

| | |
|---|---|
| **Status** | Draft v0.1 |
| **Owner** | Engineering |
| **Last updated** | 2026-11-19 |
| **Related** | [Architecture](02-architecture.md) · [PRD NFR-006/007](01-product-requirements.md#4-non-functional-requirements) |

Driveline handles personal data (identity, vehicle details, spending, optionally location and receipt images). This plan sets out how that data is protected, using the OWASP Mobile Top 10 and AWS Well-Architected Security Pillar as references.

> This document is an engineering plan, not legal advice. Have the privacy policy and terms reviewed by a qualified professional before public launch.

---

## 1. Data inventory

| Data | Sensitivity | Where stored | Retention |
|---|---|---|---|
| Email, name, sign-in identifiers | Personal | Cognito | Until account deletion |
| Vehicles, entries, reminders | Personal (financial habits) | Device SQLite, DynamoDB | Until deleted by user / account deletion |
| Receipt and vehicle photos | Personal, may include card digits or addresses | Device, S3 (private) | Until deleted; purged with account |
| Station location on fill-ups | Location (low precision) | Device, DynamoDB | With the entry |
| GPS trip routes (v2, opt-in) | **Sensitive** location history | Device, DynamoDB | User-configurable; default 12 months |
| Shared prices (v3, opt-in) | Anonymous | DynamoDB, not linked to user ID | 30 days |
| Push tokens | Device identifier | DynamoDB | Until invalid or sign-out |
| Subscription status | Commercial | RevenueCat, DynamoDB | Per store requirements |
| Crash reports | Technical | Sentry (PII scrubbing on) | 90 days |

**Data minimisation:** we do not collect VINs, licence plates, contacts or advertising identifiers in v1–v3. No ads, no selling or sharing data with data brokers.

## 2. Threat model (STRIDE summary)

| Threat | Example | Mitigation |
|---|---|---|
| **Spoofing** | Stolen tokens used to access another account | Cognito short-lived tokens, refresh token rotation, tokens in Keychain/Keystore only |
| **Tampering** | Client forges a Pro entitlement or edits a shared vehicle as a viewer | All authorisation enforced in AppSync rules and Lambdas; entitlement written only by verified webhook |
| **Repudiation** | Dispute over who changed a shared entry | `lastModifiedBy` and `updatedAt` on every record; audit fields kept on tombstones |
| **Information disclosure** | One user reads another's receipts | Per-user S3 prefixes with identity-based access; pre-signed URLs expire in ≤ 5 min; no public buckets (Block Public Access on) |
| **Denial of service / cost abuse** | Script hammers OCR or sync to run up AWS bill | AppSync and Lambda throttling, per-user scan quota, AWS WAF rate rules if needed, budget alarms |
| **Elevation of privilege** | Lambda role too broad | Least-privilege IAM per function; IAM Access Analyzer; no wildcard actions in production |

A fuller threat model is reviewed at the start of each major release (v1.5 sharing and v2 location get a dedicated session).

## 3. Application security controls

| Area | Control |
|---|---|
| Transport | HTTPS/TLS 1.2+ only; AWS endpoints only |
| At rest (cloud) | DynamoDB and S3 encryption (AWS-managed KMS keys) |
| At rest (device) | Data inside app sandbox; tokens in `expo-secure-store`; option for app lock with biometrics (Should, v1.5) |
| Input validation | Zod schemas on all API inputs, imports and file uploads; file type and size checks before S3 upload |
| Imports | CSV parsing in the domain package; formulas neutralised on export (CSV injection) |
| Dependencies | Renovate updates, `pnpm audit` and CodeQL in CI; licences checked (no copyleft in the app bundle without review) |
| Secrets | None in the repo; gitleaks in CI; Amplify secrets / Secrets Manager for API keys (RevenueCat webhook secret, Expo access token) |
| Webhooks | Verify authorisation header, idempotency keys, reject replays |
| Logging | No personal data, tokens or receipt content in logs; Sentry PII scrubbing |

## 4. Privacy by design

- **Guest mode** lets people use the app with no account and no data leaving the device.
- **Location** is never requested at install. Each location feature (station tagging, GPS trips, price sharing) is a separate, explained opt-in, revocable in settings.
- **Export and delete:** full CSV export any time (FR-060) and in-app account deletion (FR-005), as required by Apple's guidelines.
- **Store disclosures:** Apple privacy nutrition labels and Google Play Data safety form completed from the inventory in §1 and kept in sync each release.
- **Privacy policy and terms** published at a public URL before beta, written in plain language.
- **Regulations to review before launch:** California CCPA/CPRA; Washington state privacy law; Canada PIPEDA; GDPR if EU users are accepted.

## 5. AWS account security

| Control | Detail |
|---|---|
| Account structure | AWS Organizations: management account (billing only), **dev** account, **prod** account |
| Root user | MFA on, no access keys, used only for break-glass tasks |
| Human access | IAM Identity Center users with MFA; no long-lived IAM user keys |
| CI access | GitHub OIDC → per-account deploy roles, scoped to the repo and environment |
| Guardrails | CloudTrail (organisation trail), GuardDuty, AWS Config basics, Security Hub foundational checks in prod |
| Cost | AWS Budgets alerts at 50/80/100%; anomaly detection on |
| Region | us-west-2 for all workloads; other regions restricted by SCP once stable |

## 6. Security in the lifecycle

| Phase | Activity |
|---|---|
| Requirements | Security and privacy acceptance criteria on stories that touch data |
| Design | Threat model for new data flows; ADR notes security consequences |
| Build | Secure coding standards; PR checklist security items |
| Verify | CI scanning (CodeQL, audit, gitleaks); integration tests for auth rules (e.g. viewer cannot edit) |
| Release | Release checklist: store disclosures, privacy policy current, no debug flags |
| Operate | Alarms, GuardDuty findings reviewed weekly, incident process in [Engineering Standards §8](03-engineering-standards.md#8-operations) |

## 7. Vulnerability reporting

See [`SECURITY.md`](../SECURITY.md) in the repository root.

## 8. Change log

| Version | Date | Change |
|---|---|---|
| 0.1 | 2026-11-19 | Initial draft |
