---
title: "Proposal"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

## Workshop report

### 1. Project proposal

- Project overview: Build a fully serverless platform for automated plant leaf disease diagnosis from end-user uploaded images.
- Problem statement: In agriculture, leaf disease misdiagnosis is common (for example, confusing red spider mite damage on cacao leaves with fungal diseases such as VSD or Anthracnose). The system needs a reliable cloud platform where users can upload images and receive accurate AI diagnosis in near real time without maintaining 24/7 servers.
- Proposed architecture: Amazon API Gateway, AWS Lambda, Amazon S3, Amazon SQS, Amazon ECR, and Amazon DynamoDB.
- Risk: Large AI models may exceed standard Lambda memory and package limits.
- Mitigation: Package the AI inference stack as a container image and run it on Lambda through Amazon ECR.

### 2. Workshop (main technical project)

#### 2.1. Overview

The Serverless AI Inference Platform enables user authentication, issues secure upload links (pre-signed URL), stores plant images in S3, and triggers an SQS-driven Lambda workflow to run AI inference.

#### 2.2. Prerequisites

- Active AWS account (Region: ap-southeast-1).
- AWS CLI, Docker, and Postman installed.
- A trained image model for diagnosis (for example, red spider mite detection model in .pt or .onnx format).

#### 2.3. Architecture and service selection

The architecture follows an event-driven and fully serverless approach:

- Amazon Cognito: Authenticates users via JWT and protects API access.
- API Gateway and Presign Lambda: Generate S3 pre-signed URL for direct client upload, improving security and bypassing API Gateway 10 MB payload limits.
- Amazon S3: Stores Raw and Processed images. S3 Lifecycle is used to transition old objects to Glacier Archive for cost optimization.
- Amazon SQS: Decouples ingestion and inference, preventing AI Lambda overload during burst uploads.
- AWS Lambda and Amazon ECR: Execute AI inference. ECR allows large container images (up to 10 GB), suitable for ML dependencies.
- Amazon DynamoDB: Stores prediction output with low-latency NoSQL access.

#### 2.4. Step-by-step implementation

Step 1: Initialize storage and database

- Create bucket s3-raw-images and configure S3 Lifecycle Policy (transition to Glacier Archive after 30 days).
- Create DynamoDB table AI_Diagnosis_Results with partition key ImageID.

Step 2: Package and deploy AI model

- Create an ECR repository. Build a Docker image with PyTorch dependencies and AI model, then push to ECR.
- Create Inference Lambda from that container image with timeout 5 minutes and memory 3008 MB.
- Attach IAM role permissions to read SQS and write DynamoDB.

Step 3: Build a secure upload flow

- Create Presign Lambda using Boto3 generate_presigned_url with put_object.
- Configure REST API on API Gateway and integrate Cognito Authorizer.

Step 4: Connect event-driven pipeline

- Create a standard SQS queue.
- Configure S3 Event Notification to send messages to SQS for s3:ObjectCreated:\* events.
- Set SQS as trigger for Inference Lambda.

Step 5: Test and validate

- Use Postman to request a pre-signed URL.
- Perform HTTP PUT to upload a red-spider-damaged cacao leaf image to S3.
- Monitor CloudWatch Logs for inference execution.
- Verify prediction records in DynamoDB.

Step 6: Clean-up

- Empty all S3 buckets.
- Delete SQS, API Gateway, DynamoDB table, ECR image, and Lambda functions to avoid extra costs.

#### 2.5. Personal contribution and reflection

- Challenges: Uploading images via API Gateway caused HTTP 413 Payload Too Large, and Lambda timed out during model initialization.
- Solutions: Redesigned the upload flow using pre-signed URL and moved AI runtime to ECR container image.
- Future work: Integrate API Gateway WebSocket to push results from DynamoDB to the client in real time instead of manual refresh or polling.
