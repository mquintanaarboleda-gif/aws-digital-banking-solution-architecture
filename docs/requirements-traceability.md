# Requirements Traceability

| Requirement | Architecture response | Status |
|---|---|---|
| Movement history / transfers | Movements Service + Transfer Service + Core/interbank adapters | Covered |
| Data from two systems | Integration Adapters to Core and complementary system | Covered |
| At least two notification mechanisms | SMS + email, push as additional channel, provider fallback | Covered |
| SPA + cross-platform mobile, two options | React/React Native; Angular/Flutter alternative | Covered |
| OAuth 2.0 and recommended flow | OIDC/OAuth 2.0 Authorization Code + PKCE | Covered |
| KYC + facial recognition | Rekognition Face Liveness + CompareFaces + KYC/AML adapter | Covered |
| User/password, fingerprint or another method | Passkey WebAuthn with local biometrics + password/MFA fallback | Covered |
| Audit database | DynamoDB + S3 Object Lock | Covered |
| Frequent-customer persistence | Cache-Aside with ElastiCache; optional CQRS/read model | Covered |
| API Gateway + at least 3 services | API Gateway + Profile/Movements/Transfers and supporting domains | Covered |
| Ecuador financial/privacy considerations | LOPDP/RGLOPDP/SB/UAFE considerations and security/privacy controls | Covered, subject to formal validation |
| High availability / fault tolerance | Multi-AZ, autoscaling, queues, circuit breakers, redundant connectivity | Covered |
| Disaster recovery | Multi-region Warm Standby, replication and failover | Covered as design hypothesis |
| Security / monitoring | WAF/Shield/KMS/Secrets + Security Hub/GuardDuty/CloudWatch/CloudTrail/Config | Covered |
| Scalability / decoupling | ECS Auto Scaling + EventBridge/SQS + domain separation | Covered |
| Low latency | CloudFront, cache and private connectivity; Region selected by benchmark | Covered with validation |
| C4 through components | Context, Containers, Transfer Components, Onboarding Components | Covered |
