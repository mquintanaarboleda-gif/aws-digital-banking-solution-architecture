# Architecture Diagram Index

The five diagrams below were extracted directly from the anonymized source report.

## 1. C4 — System Context

![C4 System Context](../assets/diagrams/01-c4-system-context.png)

Shows customer channels, operations/compliance/SOC, the AWS digital-banking platform and external authoritative/supporting systems.

## 2. C4 — Containers and AWS Services

![C4 Containers](../assets/diagrams/02-c4-containers-aws.png)

Shows the web/mobile channels, Cognito, API Gateway, Fargate services, cache, Step Functions/Rekognition, EventBridge/SQS, DynamoDB/S3, banking systems and notification channels.

## 3. C4 — Transfer Service Components

![Transfer Components](../assets/diagrams/03-transfer-service-components.png)

Focuses on authorization, idempotency, Step Functions orchestration, Core/interbank adapters and event publication.

## 4. C4 — Onboarding / KYC Components

![Onboarding / KYC Components](../assets/diagrams/04-onboarding-kyc-components.png)

Separates privacy/consent, KYC orchestration, Face Liveness, face comparison, optional document/AML verification, Core/Cognito provisioning and passkey enrollment.

## 5. AWS Infrastructure, Hybrid Connectivity and DR

![AWS Infrastructure and DR](../assets/diagrams/05-aws-infrastructure-dr.png)

Shows the three-AZ primary environment, private runtime, security/observability services, hybrid connectivity and the Warm Standby DR Region.
