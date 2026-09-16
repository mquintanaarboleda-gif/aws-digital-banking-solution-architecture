# Architecture Diagram Index

The portfolio exposes five architecture views directly in the repository README using Mermaid, so reviewers can inspect them without downloading additional tools.

| # | View | Purpose |
|---:|---|---|
| 1 | C4 Level 1 — System Context | Shows customer, operations/compliance and the external systems around the AWS digital-banking platform |
| 2 | C4 Level 2 — Containers and AWS Services | Shows digital channels, API façade, Fargate services, events, data, identity and hybrid integration |
| 3 | C4 Level 3 — Transfer Service Components | Shows authorization, idempotency, Step Functions orchestration, Core/interbank adapters and downstream events |
| 4 | C4 Level 3 — Onboarding / KYC Components | Shows consent, KYC orchestration, Rekognition, external document/AML validation, provisioning and passkey enrollment |
| 5 | AWS Infrastructure, Hybrid Connectivity and DR | Shows Multi-AZ Fargate deployment, private Core connectivity and multi-region Warm Standby |

[Open the visual architecture in the README](../README.md#business-context)

## Original report

The anonymized Word report contains the original diagram images and full 15-page technical proposal:

[Open the anonymized Word report](Banco_Digital_Arquitectura_AWS_Milton_Quintana.docx)
