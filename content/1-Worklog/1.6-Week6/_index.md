---
title: "Week 6: API Gateway Security"
date: 2026-06-15
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Week 6 Objectives:

- Open a secure API gateway for Web App communication.
- Integrate authentication to block unauthorized access.

### Tasks:

| Day | Task                                                         | Start Date | End Date   | Resources    |
| :-- | :----------------------------------------------------------- | :--------- | :--------- | :----------- |
| Mon | - Create a REST API on Amazon API Gateway.                   | 2026-06-15 | 2026-06-15 | API Gateway  |
| Tue | - Code Lambda function to generate Pre-signed URLs for S3.   | 2026-06-16 | 2026-06-16 | Boto3 Docs   |
| Wed | - Link the `/presign` API endpoint with the Lambda function. | 2026-06-17 | 2026-06-17 | AWS API      |
| Thu | - Configure Cross-Origin Resource Sharing (CORS).            | 2026-06-18 | 2026-06-18 | Web Security |
| Fri | - Integrate Cognito Authorizer enforcing JWT Tokens.         | 2026-06-19 | 2026-06-19 | AWS Cognito  |

### Outcomes:

- Secure Upload Mechanism: Clients receive temporary authorization (Pre-signed URL) to upload files directly to S3.
- External unauthorized access successfully blocked (`401 Unauthorized`).
