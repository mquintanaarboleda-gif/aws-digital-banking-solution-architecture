# Open Risks & Production Preconditions

This portfolio intentionally distinguishes architecture proposals from production facts.

| Open item | Why it matters | Validation required |
|---|---|---|
| AWS Region | Geographic distance alone does not determine end-to-end latency. | Benchmark p50/p95/p99 from real bank sites/ISPs and through the private Core path. |
| BIA / RTO / RPO | DR capacity and replication should follow business impact, not an arbitrary diagram. | Business, continuity, risk and operations approval. |
| Core API behavior | Availability, idempotency and reconciliation depend on the authoritative platform. | Confirm API semantics, stable references, timeout behavior and reconciliation endpoints. |
| Interbank rail behavior | An uncertain timeout can create duplicate monetary effects if handled incorrectly. | Confirm transaction references, status queries, settlement states and retry rules. |
| Real load profile | Service sizing and scaling need evidence. | TPS, concurrency, payload sizes, peak periods and dependency limits. |
| Cognito / identity operating model | Identity design depends on user lifecycle, recovery and federation requirements. | Security and IAM validation, including recovery and failover procedures. |
| Biometric processing | Facial data is sensitive and requires minimization, retention and legal basis. | Privacy impact assessment and legal/compliance approval. |
| Cross-border data processing | Region/provider selection may affect data-transfer obligations. | Privacy/legal assessment of data flows and contracts. |
| Notification providers | SMS/email delivery and local coverage can vary. | Provider SLAs, delivery reporting and fallback testing. |
| DR operability | A diagram does not prove recoverability. | Game days, failover/failback and reconciliation testing. |
| DevSecOps/SRE maturity | IaC, deployment strategies, monitoring and incident response require operating discipline. | Ownership, runbooks, CI/CD controls and on-call model. |
