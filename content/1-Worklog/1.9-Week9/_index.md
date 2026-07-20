---
title: "Week 9: S3, SQS, and Lambda Pipeline"
date: 2026-06-12
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Week 9 Objectives

- Complete the event-driven image-processing pipeline.
- Configure retries, timeouts, and per-image processing safely.

### Tasks

| Date | Task | Evidence |
| :--- | :--- | :--- |
| Jun 12 | Create the inference queue, visibility timeout, and bucket/account-restricted queue policy. | SQS attributes and policy |
| Jun 15 | Configure `s3:ObjectCreated:*` from the raw bucket to SQS. | S3 notification configuration |
| Jun 16 | Create the SQS event source mapping with batch size `1`. | Mapping state is `Enabled` |
| Jun 17 | Test a valid leaf: Lambda writes `COMPLETED` and a processed image. | DynamoDB item, S3 object, CloudWatch log |
| Jun 18 | Test a non-leaf: the Rekognition gate writes `REJECTED` without a processed image. | DynamoDB item and test log |

### Outcomes

- S3 → SQS → Lambda → DynamoDB works for both `COMPLETED` and `REJECTED` paths.
- The queue drains after stable processing.
