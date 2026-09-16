# Open Risks and Production Preconditions

This is a portfolio architecture exercise. The following items must be validated before any production implementation.

| Open item | Why it matters | Required evidence / action |
|---|---|---|
| AWS Region selection | Geographic proximity alone does not guarantee latency | Measure p50/p95/p99 from real user networks and private Core paths |
| BIA / RTO / RPO | DR tier and replication cannot be finalized without business targets | Approved Business Impact Analysis and recovery objectives |
| Core API behavior | Monetary correctness depends on authoritative-system semantics | Confirm idempotency, reference, timeout, reconciliation and status APIs |
| Interbank rail semantics | Unknown outcomes can create duplicate financial effects | Define stable reference and reconciliation behavior |
| Real TPS / concurrency | Fargate, API, DynamoDB and queue sizing need workload evidence | Load model, peak traffic, growth and performance tests |
| Identity recovery | Passkeys/MFA are only as strong as recovery flows | Define enrollment, recovery, fraud controls and support process |
| Biometric data handling | Facial data is sensitive and high-risk | DPIA, legal basis, minimization, encryption, retention and access model |
| Cross-border data processing | Region/provider choices may trigger privacy obligations | Legal/privacy review and documented transfer mechanism |
| Direct Connect business case | Redundancy adds recurring cost and operational complexity | Traffic, latency, availability and cost justification |
| Operational maturity | Multi-region and event-driven systems require disciplined operations | Runbooks, game days, observability, SRE ownership and on-call model |
| Regulatory approval | Architecture cannot self-certify compliance | Formal review by risk, legal, privacy, security and relevant banking governance |
