# Architecture Decisions

| Decision | Selected approach | Rationale / trade-off |
|---|---|---|
| Web/mobile stack | React + TypeScript / React Native | Favors delivery speed and a more unified skills base; Angular + Flutter remains a valid organizational alternative. |
| OAuth flow | Authorization Code + PKCE | Appropriate for public clients such as SPA/mobile; avoids embedding a client secret. |
| Customer identity | Amazon Cognito | Centralizes OIDC/OAuth2, MFA and passkey/WebAuthn capabilities. |
| Login biometrics | Passkeys/WebAuthn with local device biometrics | Device biometric templates should not be copied to the cloud for login. |
| Remote onboarding | Rekognition Face Liveness + CompareFaces + external KYC/AML adapter | Separates AWS facial checks from replaceable document/AML providers. |
| API ingress | API Gateway Regional → VPC Link V2 → internal ALB | Keeps the service runtime private while retaining API controls and observability. |
| Main runtime | ECS Fargate | Chosen over using Lambda for every service because persistent integration workloads benefit from predictable runtime behavior and operational control. |
| Transfer workflow | Step Functions Standard | Durable, auditable workflow orchestration with controlled Retry/Catch/timeout behavior. |
| Monetary idempotency | Idempotency key + DynamoDB conditional write | Prevents duplicate execution of the same transfer request. |
| Timeout handling | Reconciliation before retrying uncertain monetary effects | Avoids duplicate debits when a downstream system completed the operation but the response was lost. |
| Asynchronous backbone | EventBridge + SQS/DLQ | Decouples audit, notification and non-synchronous work from the monetary path. |
| Cache | ElastiCache Serverless / Valkey with Cache-Aside | Reduces latency/load for non-authoritative reads; does not become the source of truth for transfer authorization. |
| Read model / CQRS | Optional second stage if Core CDC exists | Useful when Core read latency becomes limiting, but not assumed without confirmed CDC support. |
| Business audit | DynamoDB append-only + S3 Object Lock | Combines operational lookup with immutable WORM evidence. |
| Primary availability | Multi-AZ | Removes single-AZ dependency for critical services. |
| Regional DR | Warm Standby | Balances recovery time, cost and operational complexity; avoids premature Active/Active. |
| Hybrid connectivity | Redundant Direct Connect + Site-to-Site VPN backup | Supports controlled private access to existing Core and internal systems. |
| Multi-account governance | AWS Organizations / Control Tower | Separates Production, NonProd, Security, Log Archive and Shared Services with centralized guardrails. |
