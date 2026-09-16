# Architecture Overview

## Scenario

This portfolio case study describes a digital-banking platform on AWS around an existing Core Banking System. The Core remains the financial system of record; the AWS layer provides digital channels, identity, orchestration, integration, notifications, audit, observability and resilience.

## C4 structure

### Level 1 — System Context

The platform connects customers and operations/compliance teams with the Core Banking System, a complementary customer-information system, an interbank service, KYC/AML capabilities and notification providers.

![C4 System Context](../assets/diagrams/01-c4-system-context.png)

### Level 2 — Containers

The solution uses React/TypeScript for the SPA, React Native for mobile, Amazon Cognito for identity, Amazon API Gateway as the API façade, ECS Fargate for domain services, EventBridge + SQS/DLQ for asynchronous integration, ElastiCache/Valkey for non-authoritative reads, DynamoDB and S3 Object Lock for audit/evidence, Step Functions Standard for transfer orchestration and Direct Connect + VPN for private Core connectivity.

![C4 Containers](../assets/diagrams/02-c4-containers-aws.png)

### Level 3 — Transfers

The transfer domain separates API validation, authorization/step-up, idempotency, orchestration, Core and interbank adapters, and event publication. A timeout with an uncertain monetary outcome enters reconciliation rather than blind retry.

![Transfer Service Components](../assets/diagrams/03-transfer-service-components.png)

### Level 3 — Onboarding / KYC

The onboarding domain separates consent/privacy, KYC orchestration, Amazon Rekognition Face Liveness / face comparison, optional external document/AML validation, Core provisioning, Cognito provisioning and passkey enrollment.

![Onboarding / KYC Components](../assets/diagrams/04-onboarding-kyc-components.png)

## Deployment, availability and DR

The primary AWS environment spans multiple Availability Zones. Microservices run privately behind an internal ALB and API Gateway/VPC Link. Hybrid connectivity uses redundant Direct Connect where justified, with Site-to-Site VPN as backup. The exercise proposes a Warm Standby secondary Region and controlled failover through Route 53.

![AWS Infrastructure and DR](../assets/diagrams/05-aws-infrastructure-dr.png)

## Core architecture principle

The AWS digital layer improves experience and resilience without replacing the authoritative banking ledger. Financial authorization remains tied to the Core, while cache, events and read models are used only where their consistency characteristics are acceptable.
