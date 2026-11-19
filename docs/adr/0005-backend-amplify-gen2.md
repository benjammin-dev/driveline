# ADR-0005: Backend on AWS Amplify Gen 2

- **Status:** Proposed
- **Date:** 2026-11-19
- **Related:** NFR-005, NFR-006, NFR-011, FR-002, FR-072

## Context

The backend must provide sign-in, an authorised data API, file storage and scheduled jobs; cost almost nothing at low usage; and be manageable by one developer. The owner already has an AWS account.

## Options considered

1. **Containers or servers (ECS/EC2 + RDS)** — flexible; always-on cost and more operations.
2. **Hand-written AWS CDK** — full control; more boilerplate for auth, API and storage.
3. **Amplify Gen 2** — TypeScript-defined backend (auth, data, storage, functions) generating Cognito, AppSync, DynamoDB, S3 and Lambda via CDK under the hood; per-developer sandboxes and branch deployments; escape hatch to raw CDK for anything custom.

## Decision

Use **Amplify Gen 2** in **us-west-2**:

- **Auth:** Cognito user pool with email, Sign in with Apple and Google.
- **Data:** AppSync GraphQL with DynamoDB tables in on-demand mode; authorisation rules defined in the schema; custom queries/mutations for sync implemented as Lambda or JS resolvers.
- **Storage:** S3 with per-user path rules.
- **Functions:** Lambda in TypeScript (Node.js LTS runtime).
- **Escape hatch:** EventBridge Scheduler, alarms, budgets and anything else Amplify does not cover are added with CDK in `amplify/backend.ts`.

Do **not** use Amplify Gen 1 or DataStore (see ADR-0004).

## Consequences

- Serverless, pay-per-use costs; no servers to patch.
- Backend defined in TypeScript, versioned with the app.
- Some coupling to Amplify conventions; mitigated because the generated resources are standard AWS services reachable through CDK.
- Production is deployed into a separate AWS account from development (see [Security & Privacy](../04-security-privacy.md)).
