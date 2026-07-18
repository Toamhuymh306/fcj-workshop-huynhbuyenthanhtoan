---
title: "Architecture overview"
date: 2026-07-18
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Solution architecture

![KTS Smart Agri architecture](/images/5-Workshop/5.1-Workshop-overview/diagram1.png)

The main flow is:

1. A user registers, verifies their email, and receives an ID token from Amazon Cognito.
2. The frontend calls `POST /presign` through API Gateway with the ID token.
3. Presign Lambda creates a time-limited upload URL bound to `user-id` metadata.
4. The browser uploads the image directly to the raw S3 bucket.
5. S3 sends an `ObjectCreated` event to SQS; SQS invokes Inference Lambda.
6. Inference Lambda calls Rekognition `DetectLabels` and checks for `Leaf` and `Plant`.
7. Valid images run through ResNet-50; invalid images receive `REJECTED` status.
8. Lambda stores the processed image in S3 and the result in DynamoDB.
9. The frontend uses the Results API to read or delete only the current user's history.

#### Why this architecture?

| Requirement | Design |
|---|---|
| Avoid sending large files through API Gateway | Upload directly to S3 with a pre-signed URL |
| Decouple upload from inference | Asynchronous S3 and SQS pipeline |
| Run PyTorch with a large checkpoint | Lambda container image stored in ECR |
| Reject people, animals, and documents | Rekognition Image validation gate |
| Isolate each user's data | Cognito `sub`, a DynamoDB GSI, and ownership checks |
| Minimize server operations | Managed/serverless services with usage-based billing |

#### AWS services

- **Amazon Cognito:** registration, email verification, and JWT issuance.
- **Amazon API Gateway:** Presign API and Results API endpoints.
- **AWS Lambda:** URL generation, inference, and history management.
- **Amazon S3:** raw, processed, and archive image storage.
- **Amazon SQS:** upload event buffering, retries, and decoupling.
- **Amazon ECR:** Lambda container image registry.
- **Amazon Rekognition Image:** leaf-image validation.
- **Amazon DynamoDB:** result storage and `user_id` queries.
- **Amazon CloudWatch:** logs, metrics, and troubleshooting.

{{% notice tip %}}
Never include access keys, secret keys, JWTs, or unexpired pre-signed URLs in workshop screenshots.
{{% /notice %}}
