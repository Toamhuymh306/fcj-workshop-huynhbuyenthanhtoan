---
title: "Week 6: API Gateway Security"
date: 2026-05-22
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
| Fri | - Create a REST API in Amazon API Gateway.                    | 2026-05-22 | 2026-05-22 | API Gateway  |
| Mon | - Complete the Lambda function that issues pre-signed URLs.  | 2026-05-25 | 2026-05-25 | Boto3 Docs   |
| Tue | - Connect the `/presign` and `/results` endpoints.           | 2026-05-26 | 2026-05-26 | AWS API      |
| Wed | - Configure CORS and verify preflight requests.              | 2026-05-27 | 2026-05-27 | Web Security |
| Thu | - Integrate a Cognito Authorizer and verify JWT claims.      | 2026-05-28 | 2026-05-28 | AWS Cognito  |

### Outcomes:

- Secure Upload Mechanism: Clients receive temporary authorization (Pre-signed URL) to upload files directly to S3.
- Requests with missing or invalid tokens are rejected with `401 Unauthorized`.
