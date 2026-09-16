# Architecture Overview

## 1. Scope

The solution covers:

- Customer, product and account information queries.
- Movement history.
- Own-account and interbank transfers.
- Digital onboarding with facial identity verification/KYC.
- OAuth 2.0 / OIDC authentication for SPA and mobile channels.
- At least two notification mechanisms.
- Business audit and immutable evidence retention.
- Integration with the Core Banking System and a complementary customer-information system.
- High availability, disaster recovery, security, monitoring and observability.

## 2. Architectural principles

1. **Core as source of truth** — financial authorization is not made from cache data alone.
2. **Zero Trust / least privilege** — strong authentication, scope/role-based authorization and private workloads.
3. **Decoupling** — events and queues isolate notification, audit and other asynchronous effects.
4. **Idempotency** — monetary operations use a unique business reference and idempotency key.
5. **Fail-safe integration** — timeouts, circuit breakers, backoff and DLQ are used selectively; uncertain financial effects are reconciled rather than blindly retried.
6. **Observability by design** — correlation IDs, traces, metrics and audit events are part of the first release.
7. **Privacy by design** — data minimization, explicit retention, encryption and separation of duties.
8. **Infrastructure as Code** — reproducible and auditable environments.

## 3. C4 Level 1 — System Context

![System Context](../assets/diagrams/01-c4-system-context.png)

The digital-banking platform sits between the customer channels and authoritative banking systems. Operations, compliance and SOC teams observe and audit the environment. External dependencies include Core Banking, customer-detail services, interbank rails, KYC/AML providers and notification channels.

## 4. C4 Level 2 — Containers

![Containers](../assets/diagrams/02-c4-containers-aws.png)

The main containers and services are:

- React/TypeScript SPA on S3 + CloudFront.
- React Native mobile application.
- Amazon Cognito for customer identity.
- Amazon API Gateway as API façade.
- ECS Fargate services for Profile, Movements, Transfers, Onboarding, Integration, Notification and Audit.
- EventBridge + SQS/DLQ for asynchronous integration.
- ElastiCache Serverless / Valkey for Cache-Aside.
- DynamoDB and S3 Object Lock for operational audit/idempotency and immutable evidence.
- Step Functions Standard for transfer workflows.
- Amazon Rekognition for Face Liveness / face comparison.
- Direct Connect + VPN for private integration with existing banking systems.

## 5. C4 Level 3 — Transfer components

![Transfer Components](../assets/diagrams/03-transfer-service-components.png)

The Transfer domain separates API validation, authorization/step-up, idempotency, orchestration, Core/interbank integration, result mapping and event publication. The design explicitly avoids repeating an uncertain debit after a timeout until reconciliation determines the actual state.

## 6. C4 Level 3 — Onboarding/KYC components

![Onboarding/KYC Components](../assets/diagrams/04-onboarding-kyc-components.png)

The onboarding flow separates privacy/consent, KYC orchestration, liveness, facial comparison, optional AML/document validation, Core provisioning, Cognito provisioning and passkey enrollment.

## 7. Deployment and DR

![AWS Infrastructure and DR](../assets/diagrams/05-aws-infrastructure-dr.png)

The primary Region uses three AZs for critical workloads. Microservices remain private behind API Gateway, VPC Link and an internal ALB. Direct Connect and VPN provide hybrid connectivity. The DR design is Warm Standby in a second Region with selected replication for identity, DynamoDB and S3 evidence.
