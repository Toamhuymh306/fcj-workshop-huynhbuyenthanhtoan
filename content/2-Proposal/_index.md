---
title: "Proposal"
date: 2026-07-20
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

## KTs Smart Agriculture

### A serverless crop-disease detection platform on AWS

**Source code:** [github.com/Toamhuymh306/kts-smart-agri](https://github.com/Toamhuymh306/kts-smart-agri)

## 1. Executive summary

KTs Smart Agriculture is a web application that helps identify crop diseases from leaf images. Users register and verify their email through Amazon Cognito, upload images directly to Amazon S3 through pre-signed URLs, and an S3 → SQS → Lambda container pipeline validates each image and runs a ResNet-50 model trained on PlantVillage. User-scoped results are stored in Amazon DynamoDB, and annotated images are stored in a processed bucket for display in the frontend.

The solution uses a serverless, event-driven architecture to avoid fixed-server operations, decouple uploads from AI processing, and control scaling through SQS/Lambda concurrency. The project objective is a testable system that can be delivered within the 12-week internship; it is not presented as a replacement for professional field diagnosis.

## 2. Problem and scope

### 2.1. Problem

- Sending images through a traditional backend increases bandwidth use and can hit API payload limits.
- PyTorch and model weights are not a good fit for Lambda ZIP deployment-package limits.
- AI inference has variable duration; synchronous processing can cause frontend timeouts.
- Results must be isolated by user, with no cross-user read or delete access.

### 2.2. Proposed solution

- API Gateway and Presign Lambda issue short-lived pre-signed URLs; the browser PUTs images directly into the raw S3 bucket.
- S3 Event Notification sends messages to SQS to decouple upload from inference and support retries.
- Inference Lambda uses a container image in ECR, runs a Rekognition validation gate, and then performs ResNet-50 inference.
- DynamoDB stores status, predictions, and ownership; Results Lambda exposes APIs to list, read, and delete history.
- CloudWatch centralizes logs and metrics. CloudFront, WAF, CloudTrail, and SNS alarms are optional production-hardening layers and are described as deployed only when configuration evidence exists.

### 2.3. AI scope

- Dataset: PlantVillage, covering 14 crops and 38 healthy/disease classes.
- Production model: ResNet-50; LeNet remains a benchmark model.
- Input: images resized to `224 × 224` and normalized with ImageNet statistics.
- Validation gate: the classifier runs only when Amazon Rekognition detects both `Leaf` and `Plant` above the configured threshold.
- Output: crop, disease, confidence, top-k results, processing time, and an annotated image.

## 3. Solution architecture

![KTs Smart Agriculture architecture](/images/2-Proposal/diagram1.png)

### 3.1. Main workflow

1. The user registers, verifies their email, and receives a JWT from Amazon Cognito.
2. The frontend calls `POST /presign` through API Gateway with the JWT.
3. Presign Lambda reads `sub` from Cognito claims and creates an `image_id`, S3 key, and pre-signed URL with signed `user-id` metadata.
4. The browser PUTs the image directly into the raw S3 bucket; image bytes do not pass through the API Gateway/Lambda payload.
5. S3 sends `s3:ObjectCreated:*` to the inference SQS queue.
6. The SQS event source mapping invokes Inference Lambda with batch size `1`.
7. Lambda calls Rekognition `DetectLabels`. Invalid images are stored as `REJECTED`; valid images continue to ResNet-50 inference.
8. Lambda writes the result to DynamoDB and saves the annotated image in the processed S3 bucket.
9. The frontend calls Cognito-protected Results Lambda APIs to list, read, or delete resources owned by the current user.
10. CloudWatch collects logs and metrics; alarms can publish SNS notifications in the production configuration.

### 3.2. AWS components

| Component                        | Responsibility                                                      | Project status                |
| -------------------------------- | ------------------------------------------------------------------- | ----------------------------- |
| Amazon Cognito                   | Signup, email verification, login, and JWT issuance                 | Core                          |
| Amazon API Gateway               | REST routes for `/presign`, `/results`, and `/results/{scanId}`     | Core                          |
| Presign Lambda                   | Create pre-signed URLs and signed ownership metadata                | Core                          |
| Inference Lambda                 | Rekognition gate, ResNet-50 inference, result persistence           | Core                          |
| Results Lambda                   | List, read, and delete owner-scoped history                         | Core                          |
| Amazon S3                        | Raw and processed images; a frontend bucket when using S3 hosting   | Core/deployment-dependent     |
| Amazon SQS                       | Buffer S3 events, support retries, and control concurrency          | Core                          |
| Amazon ECR                       | Store the Lambda container image; not part of the runtime data path | Core                          |
| Amazon DynamoDB                  | `image_id` partition key, `user_id-index` GSI, and TTL              | Core                          |
| Amazon Rekognition               | Reject non-leaf images before classification                        | Core                          |
| Amazon CloudWatch                | Logs, metrics, alarms, and troubleshooting                          | Core                          |
| CloudFront, WAF, CloudTrail, SNS | CDN, web/API protection, audit, and alarm notifications             | Optional production hardening |

## 4. Security and reliability requirements

- Keep raw and processed buckets private; use only short-lived pre-signed URLs.
- Protect API Gateway with a Cognito User Pools Authorizer. The backend derives the owner from JWT `sub`, never from a caller-controlled `user_id`.
- Results Lambda verifies ownership before reading/deleting a DynamoDB record or S3 object.
- Apply least privilege to each Lambda role; do not use `AdministratorAccess` or `s3:*`.
- Restrict production CORS to the actual frontend origin.
- Restrict the SQS queue policy with `aws:SourceArn` and `aws:SourceAccount`.
- Set visibility timeout to at least six times the Lambda timeout; the current design uses `720` seconds for a `120`-second function timeout.
- Use `image_id` for idempotency; add a DLQ and alarm for repeatedly failing messages.
- Set maximum concurrency to protect account quotas and prevent inference from consuming uncontrolled resources.

## 5. The team's shared 12-week implementation plan

The milestones below describe the project's overall schedule. Each member prepares a separate Worklog based on their actual role and completed tasks while remaining aligned with these shared milestones.

| Week | Dates         | Workstream                    | Main deliverable                         |
| ---: | ------------- | ----------------------------- | ---------------------------------------- |
|    1 | Apr 17–23     | Kickoff, IAM, AWS CLI, Budget | Environment and backlog                  |
|    2 | Apr 24–30     | PlantVillage preparation      | Dataset split and preprocessing pipeline |
|    3 | May 01–07     | LeNet/ResNet training         | Checkpoints and evaluation report        |
|    4 | May 08–14     | Docker and ECR                | Container image and local smoke test     |
|    5 | May 15–21     | S3, DynamoDB, Lambda backend  | Presign/results handlers and storage     |
|    6 | May 22–28     | API Gateway and Cognito       | JWT-protected API and CORS               |
|    7 | May 29–Jun 04 | Frontend                      | Authentication, upload, and history UI   |
|    8 | Jun 05–11     | End-to-end MVP                | Basic workflow and defect backlog        |
|    9 | Jun 12–18     | S3 → SQS → Lambda             | `COMPLETED` and `REJECTED` paths         |
|   10 | Jun 19–25     | Security/integration tests    | Ownership and deletion tests             |
|   11 | Jun 26–Jul 02 | CloudWatch and optimization   | Metrics, runbook, and cost review        |
|   12 | Jul 03–10     | Acceptance and handover       | Release, workshop, demo, and report      |

## 6. Acceptance criteria

- Signup, email verification, login, and logout work correctly.
- The API rejects missing/invalid JWTs; User A cannot read or delete User B's resources.
- Valid JPEG, PNG, and WebP files upload directly to S3 through pre-signed URLs.
- A valid leaf creates a `COMPLETED` record and a processed image.
- A non-leaf creates a `REJECTED` record and no processed image.
- The SQS queue drains after stable processing; repeatedly failing messages move to a DLQ.
- Final CloudWatch logs contain no unresolved checkpoint, metadata, environment-variable, or timeout errors.
- Source code, deployment instructions, bilingual workshop, test report, and demo are handed over.

## 7. Cost-estimation approach

Do not present “under USD 1/month” as a commitment. Cost must be calculated for `ap-southeast-1`, the account's Free Tier status, and measured runtime data.

**Estimation assumptions:** 1,000 images/month; stated average image size; Inference Lambda at `2048 MB`, `120`-second timeout, batch size `1`; average and p95 duration taken from CloudWatch after load testing.

| Service            | Cost driver for AWS Pricing Calculator                                                 |
| ------------------ | -------------------------------------------------------------------------------------- |
| Lambda             | Requests, GB-seconds, architecture, average/p95 duration, additional ephemeral storage |
| Rekognition        | Number of `DetectLabels` images                                                        |
| S3                 | Raw/processed storage, PUT/GET, lifecycle transitions, retrieval, and data transfer    |
| SQS                | Send/Receive/Delete requests and retries                                               |
| DynamoDB           | On-demand reads/writes, storage, and backups                                           |
| API Gateway        | API calls and data transfer                                                            |
| ECR                | Container-image storage and any cross-Region transfer                                  |
| CloudWatch         | Log ingestion/retention, custom metrics, and alarms                                    |
| CloudFront/WAF/SNS | Included only when optional production layers are enabled                              |

S3 Glacier Flexible Retrieval and Deep Archive are suitable only for data that does not require immediate access. Include minimum storage duration, transition/retrieval requests, and early-deletion charges. For small images or user-visible history, expiration or S3 Intelligent-Tiering may be more appropriate than a fixed 30-day archive transition.

## 8. Risks and mitigations

| Risk                                   | Impact                              | Mitigation                                                                                                         |
| -------------------------------------- | ----------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| Slow cold start/model loading          | High latency or timeout             | Reduce image/model size, measure p95, tune memory/timeout; use Provisioned Concurrency only when justified by cost |
| Images outside the PlantVillage domain | Incorrect prediction                | Rekognition gate, capture guidance, confidence threshold, additional field data                                    |
| SQS at-least-once delivery             | Duplicate results                   | `image_id` idempotency, conditional writes, and DLQ                                                                |
| Sudden traffic growth                  | Throttling or unexpected cost       | Maximum/reserved concurrency, Budget alarms, and rate limiting/WAF in production                                   |
| Cross-user access                      | Data disclosure or deletion         | Cognito authorizer, JWT-derived owner, `user_id` GSI, and authorization tests                                      |
| Unsuitable archive policy              | Slow retrieval or retrieval charges | Select retention by use case and verify minimum durations before enabling lifecycle rules                          |

## 9. References

- [AWS Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html)
- [API Gateway quotas](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-quotas.html)
- [Using AWS Lambda with Amazon SQS](https://docs.aws.amazon.com/lambda/latest/dg/with-sqs.html)
- [Amazon S3 Lifecycle transition considerations](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-transition-general-considerations.html)
- [AWS Pricing Calculator](https://calculator.aws/)
