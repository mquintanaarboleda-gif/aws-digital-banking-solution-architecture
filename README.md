# AWS Digital Banking Solution Architecture

### Solution Architecture Case Study | AWS | C4 | Cognito | KYC | Step Functions | Fargate | HA/DR

An independent portfolio case study showing how I would design a **secure, resilient and scalable digital-banking platform on AWS** around an existing Core Banking System.

The design focuses on five architecture problems that materially affect a banking platform: **customer identity, digital onboarding/KYC, monetary-transfer orchestration, integration with authoritative systems, and operational resilience**.

> **Portfolio disclaimer:** this is an anonymized architecture exercise created for professional demonstration. The financial institution name has been replaced with the generic scenario name **Banco Digital Ecuador**. It is not an official, deployed or approved architecture of any bank. Production use would require business, risk, cybersecurity, privacy, legal, regulatory and capacity validation.

## Explore the case study

| Resource | What it shows |
|---|---|
| **[Original Anonymized Architecture Report (PDF)](docs/Reto_Banco_Digital_Arquitectura_AWS_Milton_Quintana.pdf)** | Complete architecture report in its original formatted version |
| **[Architecture Overview](docs/architecture-overview.md)** | Business context, C4 structure, services and critical flows |
| **[Architecture Decisions](docs/architecture-decisions.md)** | Key technical decisions, rationale and trade-offs |
| **[Requirements Traceability](docs/requirements-traceability.md)** | Challenge requirement → proposed AWS solution → status |
| **[Open Risks & Production Preconditions](docs/open-risks.md)** | Assumptions and decisions that require evidence before production |
| **[Diagram Index](docs/diagram-index.md)** | Five architecture views represented in Mermaid |
| **[Disclaimer](DISCLAIMER.md)** | Portfolio / non-production scope |

---

## What this project demonstrates

- C4 modeling from **System Context → Containers → Components**
- AWS digital-channel architecture around an existing Core Banking System
- OAuth 2.0 / OIDC using **Authorization Code + PKCE**
- **Amazon Cognito** with passkeys/WebAuthn and MFA fallback
- Digital onboarding using **Amazon Rekognition Face Liveness + CompareFaces**
- Durable transfer orchestration with **AWS Step Functions Standard**
- Idempotency for monetary operations using a **DynamoDB conditional-write guard**
- Domain adapters around Core Banking, interbank services and KYC/AML providers
- Event-driven decoupling with **EventBridge + SQS/DLQ**
- ECS on **AWS Fargate** for the main service runtime
- Cache-Aside with **ElastiCache Serverless / Valkey** for non-authoritative read data
- Business audit using **DynamoDB + S3 Object Lock**
- Multi-AZ availability and **multi-region Warm Standby** disaster recovery
- Direct Connect + Site-to-Site VPN for hybrid connectivity
- Security, observability, CI/CD and AWS multi-account governance

---

## Business context

The digital platform supports customer and account queries, movement history, transfers, digital onboarding, authentication, notifications and business audit while keeping the **Core Banking System as the financial system of record**.

The architecture deliberately avoids moving financial authorization into cache or satellite services. Balances, limits and final monetary effects remain dependent on the authoritative banking systems.

```mermaid
flowchart LR
  C[Customer<br/>Web / Mobile] --> DB[Digital Banking Platform<br/>AWS]
  OPS[Operations / Compliance / SOC] --> DB
  DB --> CORE[Core Banking<br/>Financial System of Record]
  DB --> DETAIL[Complementary Customer System]
  DB --> RAIL[Interbank Service]
  DB --> KYC[KYC / AML Provider]
  DB --> NOTIF[SMS / Push / Email]
```

---

## C4 container architecture

The solution uses a moderate service decomposition rather than creating dozens of microservices without a clear reason. The main domains are Profile, Movements, Transfers, Onboarding/KYC, Integration Adapters, Notification and Audit.

```mermaid
flowchart TB
  WEB[React + TypeScript SPA] --> EDGE[Route 53 + CloudFront + WAF + Shield + ACM]
  MOB[React Native Mobile App] --> APIGW[API Gateway Regional]
  EDGE --> APIGW
  APIGW --> VPCL[VPC Link V2]
  VPCL --> ALB[Internal ALB]
  ALB --> SVC[ECS Fargate Services<br/>Profile | Movements | Transfers | Onboarding | Integration | Notification | Audit]
  SVC --> CACHE[ElastiCache Serverless / Valkey]
  SVC --> SFN[Step Functions Standard]
  SVC --> EVENT[EventBridge + SQS / DLQ]
  SVC --> DDB[DynamoDB]
  SVC --> S3[S3 Object Lock]
  SVC --> COG[Amazon Cognito]
  SVC --> REK[Amazon Rekognition]
  SVC --> HYB[Direct Connect + VPN]
  HYB --> CORE2[Core / Internal Systems]
```

### Main technology choices

| Area | Decision |
|---|---|
| Web | React + TypeScript on S3/CloudFront |
| Mobile | React Native baseline; Angular + Flutter retained as an alternative stack |
| Identity | Amazon Cognito, OIDC/OAuth 2.0, Authorization Code + PKCE, passkeys/WebAuthn, MFA |
| Edge | Route 53, CloudFront, WAF, Shield Advanced, ACM |
| API | Amazon API Gateway Regional + VPC Link V2 + internal ALB |
| Runtime | Amazon ECS on Fargate, Multi-AZ |
| Orchestration | AWS Step Functions Standard |
| Event backbone | EventBridge + SQS + DLQ |
| Cache | ElastiCache Serverless / Valkey, Cache-Aside |
| Idempotency / audit | DynamoDB |
| Immutable evidence | S3 Object Lock |
| KYC / biometrics | Amazon Rekognition Face Liveness + CompareFaces + external KYC/AML adapter |
| Hybrid connectivity | Direct Connect + Site-to-Site VPN backup |
| DR | Warm Standby in a second AWS Region |

---

## Transfer architecture: consistency before convenience

The main transfer risk is not service availability by itself; it is **duplicating a monetary effect after an uncertain timeout**.

The design therefore uses a unique business reference, an idempotency key, a DynamoDB conditional-write guard and explicit reconciliation before repeating an uncertain monetary instruction.

```mermaid
flowchart LR
  API[Transfer API] --> AUTH[Authorization / Step-up]
  AUTH --> IDEM[Idempotency Guard<br/>DynamoDB conditional write]
  IDEM --> ORCH[Transfer Orchestrator<br/>Step Functions Standard]
  ORCH --> CORE[Core Banking Adapter]
  ORCH --> BANK[Interbank Adapter]
  ORCH --> EVENT[Event Publisher]
  EVENT --> AUDIT[Audit Event]
  EVENT --> NOTIF[Notification Event]
```

The flow separates validation, authorization/step-up, idempotency, orchestration, Core/interbank adapters and transactional event publication. Audit and notification are downstream effects and do not determine the success of the monetary operation.

---

## Digital onboarding and customer identity

The design separates **remote identity verification** from **subsequent login authentication**.

For onboarding, a KYC Orchestrator coordinates consent/privacy, Face Liveness, face comparison, optional document/AML validation, Core provisioning and Cognito provisioning. For later authentication, the preferred mechanism is a passkey/WebAuthn credential protected by local device biometrics rather than uploading device biometric templates to the cloud.

```mermaid
flowchart LR
  APP[Mobile Onboarding API] --> CONSENT[Consent & Privacy]
  CONSENT --> KYC[KYC Orchestrator]
  KYC --> LIVE[Face Liveness<br/>Amazon Rekognition]
  KYC --> MATCH[Face Match<br/>CompareFaces]
  KYC --> DOC[Document / AML Provider Adapter]
  KYC --> CORE[Customer Provisioning Adapter]
  KYC --> COG[Cognito Provisioning]
  COG --> PASS[Passkey Enrollment<br/>WebAuthn]
```

---

## AWS infrastructure, availability and DR

The primary environment spans three Availability Zones with private Fargate workloads, an internal ALB, managed data/event services, centralized security/observability and private connectivity to the bank.

The DR strategy is **Warm Standby**, with reduced capacity in a second Region and replication mechanisms for identity, DynamoDB and S3 evidence where applicable.

```mermaid
flowchart TB
  USERS[Internet / Customers] --> EDGE[Route 53 + CloudFront + WAF]
  EDGE --> API[API Gateway Regional]
  subgraph PRIMARY[Primary AWS Region - 3 AZ]
    API --> ALB[Internal ALB]
    ALB --> F1[ECS Fargate - AZ A]
    ALB --> F2[ECS Fargate - AZ B]
    ALB --> F3[ECS Fargate - AZ C]
    F1 --> DATA[DynamoDB / EventBridge / SQS / ElastiCache]
    F2 --> DATA
    F3 --> DATA
  end
  PRIMARY --> HYB[Transit / Direct Connect + VPN]
  HYB --> CORE[Bank Data Center / Core]
  PRIMARY -. replication .-> DR[Secondary AWS Region<br/>Warm Standby]
  EDGE -. controlled failover .-> DR
```

The proposed RTO/RPO values in the exercise are design hypotheses. Final targets must be approved through business impact analysis and tested through failover/failback exercises.

---

## Security and observability

The architecture applies layered controls across edge, customer identity, workforce identity, application, data, network and software supply chain. The operational stack includes CloudWatch, X-Ray/OpenTelemetry, CloudTrail, Config, GuardDuty, Security Hub, Inspector and Macie where applicable.

Business observability is also explicit: login failures, passkey/MFA challenges, transfer states, dependency latency, notification delivery, DLQ depth, cache hit ratio and duplicate requests blocked by idempotency are treated as operational signals.

---

## Architectural principles

> The Core Banking System remains the authority for financial truth. AWS provides the digital experience, elasticity, security controls, orchestration and integration around it.

**Authoritative data → secure identity → idempotent monetary operations → explicit integration boundaries → asynchronous side effects → measurable resilience**

---

## Author

**Milton Quintana**  
IT Infrastructure & Solutions Leader | Solution & Cloud Architecture | AWS / Azure | Technical Pre-Sales

[GitHub Profile](https://github.com/mquintanaarboleda-gif)
