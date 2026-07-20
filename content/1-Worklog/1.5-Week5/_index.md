---
title: "Week 5: Serverless Backend"
date: 2026-05-15
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Week 5 Objectives:

- Build an automated serverless workflow for image analysis.
- Set up a NoSQL database to store inference results.

### Tasks:

| Day | Task                                                              | Start Date | End Date   | Resources    |
| :-- | :---------------------------------------------------------------- | :--------- | :--------- | :----------- |
| Fri | - Design the Lambda functions and serverless backend flow.        | 2026-05-15 | 2026-05-15 | AWS Lambda     |
| Mon | - Create least-privilege IAM roles for S3 and DynamoDB.           | 2026-05-18 | 2026-05-18 | AWS IAM        |
| Tue | - Create `kts-smartagri-dev-inference-results` in DynamoDB.       | 2026-05-19 | 2026-05-19 | AWS DynamoDB   |
| Wed | - Create raw/processed S3 buckets, CORS, and lifecycle rules.     | 2026-05-20 | 2026-05-20 | AWS S3         |
| Thu | - Implement and test the presign/results Lambda handlers.        | 2026-05-21 | 2026-05-21 | Boto3/Unittest |

### Outcomes:

- The basic backend issues pre-signed URLs and stores/queries DynamoDB results; the event pipeline is completed in week 9.
