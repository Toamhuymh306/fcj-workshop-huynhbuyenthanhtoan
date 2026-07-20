---
title: "Week 11: Monitoring and Optimization"
date: 2026-06-26
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Week 11 Objectives

- Improve system observability and troubleshooting.
- Optimize the Lambda container, storage, and operating cost.

### Tasks

| Date | Task | Evidence |
| :--- | :--- | :--- |
| Jun 26 | Standardize CloudWatch Logs for presign, inference, and results Lambdas. | Log groups and samples |
| Jun 29 | Track duration, errors, throttles, queue depth, and oldest-message age. | Metrics/dashboard |
| Jun 30 | Measure cold start, inference duration, and memory use. | Performance report |
| Jul 01 | Tune container, memory/timeout, S3 lifecycle, and DynamoDB TTL. | Before/after configuration |
| Jul 02 | Complete the timeout, DLQ, CORS, and model-error runbook. | Troubleshooting guide |

### Outcomes

- Logs and metrics can trace one upload through the complete workflow.
- Cost/performance settings are documented, auditable, and repeatable.
