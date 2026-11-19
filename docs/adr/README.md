# Architecture Decision Records

An ADR records one architecturally significant decision: the context, the decision, and its consequences. ADRs are **immutable once accepted**. To change a decision, write a new ADR that supersedes the old one and update the old one's status.

Format: [Michael Nygard's template](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions), see [`template.md`](template.md).

## When to write one

Write an ADR when a change:
- adds or replaces a framework, library with wide reach, cloud service or vendor
- changes the data model, sync protocol, API contract or security model
- would be expensive to reverse

## Index

| ADR | Title | Status |
|---|---|---|
| [0001](0001-record-architecture-decisions.md) | Record architecture decisions | Accepted |
| [0002](0002-mobile-expo-react-native.md) | Build the app with Expo and React Native | Proposed |
| [0003](0003-monorepo-and-shared-domain.md) | Monorepo with a pure shared domain package | Proposed |
| [0004](0004-offline-first-sync.md) | Offline-first with SQLite and a custom sync engine | Proposed |
| [0005](0005-backend-amplify-gen2.md) | Backend on AWS Amplify Gen 2 | Proposed |
| [0006](0006-push-notifications.md) | Local notifications plus Expo Push Service | Proposed |
| [0007](0007-subscriptions-revenuecat.md) | Subscriptions through RevenueCat | Proposed |
| [0008](0008-receipt-scanning-textract.md) | Receipt scanning with Amazon Textract AnalyzeExpense | Proposed |

"Proposed" ADRs become "Accepted" when the product owner approves the architecture (end of Sprint 0).
