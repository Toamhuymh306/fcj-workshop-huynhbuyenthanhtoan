---
title: "Week 5: Serverless Backend"
date: 2026-06-08
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
| Mon | - Initialize Lambda function from the ECR model Image.            | 2026-06-08 | 2026-06-08 | AWS Lambda   |
| Tue | - Set up IAM Role granting S3 and DynamoDB access.                | 2026-06-09 | 2026-06-09 | AWS IAM      |
| Wed | - Create `kts-smartagri-dev-inference-results` table in DynamoDB. | 2026-06-10 | 2026-06-10 | AWS DynamoDB |
| Thu | - Configure the `kts-smartagri-dev-raw-images` S3 Bucket.         | 2026-06-11 | 2026-06-11 | AWS S3       |
| Fri | - Set up Event Triggers to invoke Lambda upon new image uploads.  | 2026-06-12 | 2026-06-12 | AWS Events   |

### Outcomes:

- Automated pipeline established: S3 upload -> Lambda invocation -> DynamoDB storage works flawlessly.
