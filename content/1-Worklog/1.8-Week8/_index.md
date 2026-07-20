---
title: "Week 8: End-to-End MVP"
date: 2026-06-05
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Week 8 Objectives:

- Complete an MVP that runs from authentication through result display.
- Identify integration issues before event-pipeline and security hardening.

### Tasks:

| Day | Task                                                              | Start Date | End Date   | Resources  |
| :-- | :---------------------------------------------------------------- | :--------- | :--------- | :--------- |
| Fri | - Verify login, JWT handling, and the API Gateway Authorizer.  | 2026-06-05 | 2026-06-05 | QA Testing   |
| Mon | - Verify pre-signed URLs and direct S3 image uploads.          | 2026-06-08 | 2026-06-08 | AWS S3       |
| Tue | - Run the container smoke test and invoke inference Lambda.    | 2026-06-09 | 2026-06-09 | AWS Lambda   |
| Wed | - Verify DynamoDB result writes and reads.                     | 2026-06-10 | 2026-06-10 | AWS DynamoDB |
| Thu | - Record MVP defects and update the hardening backlog.         | 2026-06-11 | 2026-06-11 | GitHub       |

### Outcomes:

- The MVP completes login → upload → inference → result storage/display.
- Defects and hardening work for weeks 9–11 are explicitly tracked.
