# Architecture Overview

## Scenario

This portfolio case study describes a digital-banking platform on AWS around an existing Core Banking System. The Core remains the financial system of record; the AWS layer provides digital channels, identity, orchestration, integration, notifications, audit, observability and resilience.

## C4 structure

### Level 1 — System Context

The platform connects customers and operations/compliance teams with the Core Banking System, a complementary customer-information system, an interbank service, KYC/AML capabilities and notification providers.

### Level 2 — Containers

The solution uses React/TypeScript for the SPA, React Native for mobile, Amazon Cognito for identity, Amazon API Gateway as the API façade, ECS Fargate for domain services, EventBridge + SQS/DLQ for asynchronous integration, ElastiCache/Valkey for non-authoritative reads, DynamoDB and S3 Object Lock for audit/evidence, Step Functions Standard for transfer orchestration and Direct Connect + VPN for private Core connectivity.

### Level 3 — Transfers

The transfer domain separates API validation, authorization/step-up, idempotency, orchestration, Core and interbank adapters, and event publication. A timeout with an uncertain monetary outcome enters reconciliation rather than blind retry.

### Level 3 — Onboarding / KYC

The onboarding domain separates consent/privacy, KYC orchestration, Amazon Rekognition Face Liveness / face comparison, optional external document/AML validation, Core provisioning, Cognito provisioning and passkey enrollment.

## Deployment, availability and DR

The primary AWS environment spans multiple Availability Zones. Microservices run privately behind an internal ALB and API Gateway/VPC Link. Hybrid connectivity uses redundant Direct Connect where justified, with Site-to-Site VPN as backup. The exercise proposes a Warm Standby secondary Region and controlled failover through Route 53.

## Visual models

The repository README renders the System Context, container architecture, transfer components, onboarding/KYC components and AWS HA/DR deployment directly as Mermaid diagrams so they can be reviewed from GitHub without additional software.

[Open the visual architecture in the README](../README.md#business-context)

## Core architecture principle

The AWS digital layer improves experience and resilience without replacing the authoritative banking ledger. Financial authorization remains tied to the Core, while cache, events and read models are used only where their consistency characteristics are acceptable.
