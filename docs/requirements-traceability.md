# Requirements Traceability

| Challenge requirement | Proposed solution | Status |
|---|---|---|
| Movement history / transfers | Movements Service + Transfer Service + Core/interbank adapters | Covered |
| Data from two systems | Integration Adapters to Core and complementary system | Covered |
| Two notification systems | SMS + email, push as additional channel, provider fallback | Covered |
| SPA + mobile with two technology options | React/React Native; Angular/Flutter alternative | Covered |
| OAuth 2.0 and appropriate flow | OIDC/OAuth2 Authorization Code + PKCE | Covered |
| KYC + facial recognition | Rekognition Face Liveness + CompareFaces + KYC/AML adapter | Covered |
| User/password, fingerprint or other login | Passkey/WebAuthn with local biometrics + password/MFA fallback | Covered |
| Audit database | DynamoDB + S3 Object Lock | Covered |
| Frequent-customer persistence | Cache-Aside with ElastiCache; optional CQRS/read model | Covered |
| API Gateway + at least three services | API Gateway + Profile/Movements/Transfers + additional services | Covered |
| Regulatory considerations | Ecuador data-protection, banking-risk and AML considerations reflected in the proposal | Covered in design; formal validation required |
| HA / fault tolerance | Multi-AZ, autoscaling, queues, circuit breakers, redundant connectivity | Covered |
| DR | Multi-region Warm Standby, replication and controlled failover | Covered |
| Security / monitoring | WAF, Shield, KMS, Secrets Manager, Security Hub, GuardDuty, CloudWatch, CloudTrail, Config | Covered |
| Scalability / decoupling | ECS Auto Scaling + EventBridge/SQS + domain separation | Covered |
| Low latency | CloudFront, cache and private connectivity; final Region selected by benchmark | Covered with validation |
| C4 through components | Context, Containers, Transfer Components and Onboarding Components | Covered |
