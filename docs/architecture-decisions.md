# Architecture Decisions

| Decision | Selected approach | Why | Main trade-off |
|---|---|---|---|
| Web / mobile | React + TypeScript / React Native | Shared language and broad ecosystem | Requires architectural discipline across teams |
| Customer identity | Amazon Cognito + OIDC/OAuth 2.0 Authorization Code + PKCE | Appropriate for SPA/mobile public clients | Identity lifecycle and recovery require governance |
| Strong login | Passkeys/WebAuthn, password + MFA fallback | Phishing resistance; local device biometrics stay local | Device/recovery UX must be designed carefully |
| Digital onboarding | Rekognition Face Liveness + CompareFaces + external KYC/AML adapter | Separates AWS biometric capability from replaceable compliance provider | Biometric/privacy controls and DPIA required |
| API façade | API Gateway Regional + VPC Link + internal ALB | Controlled edge, routing, throttling and private backend | Additional hop and operational configuration |
| Runtime | ECS on Fargate | Managed runtime with predictable integration behavior | Higher steady-state cost than some serverless patterns |
| Transfer orchestration | Step Functions Standard | Durable, auditable workflow for long-running transfer steps | Workflow design must not replace domain idempotency |
| Monetary idempotency | DynamoDB conditional-write guard + unique business reference | Prevents duplicate request execution | Requires expiry, replay and reconciliation rules |
| Async integration | EventBridge + SQS + DLQ | Decouples audit, notification and background effects | Eventual consistency and duplicate delivery handling |
| Cache | ElastiCache Serverless / Valkey, Cache-Aside | Reduces Core latency/load for safe reads | Staleness; cannot become financial source of truth |
| Audit | DynamoDB append-only + S3 Object Lock | Operational search + immutable evidence | Retention and cost governance required |
| Hybrid connectivity | Direct Connect + Site-to-Site VPN backup | Private, resilient Core access | Cost and routing/BGP complexity |
| Primary availability | Multi-AZ | Removes single-AZ dependency | Does not solve regional disaster |
| DR | Warm Standby in second Region | Balances recovery time, cost and complexity | Final sizing depends on approved BIA/RTO/RPO |
| Governance | Organizations / Control Tower + Security + Log Archive + IaC | Separation of duties and reproducibility | Requires platform/governance maturity |
