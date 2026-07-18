---
title: "Proposal"
date: 2026-07-08
weight: 2
chapter: false
hidden: true
pre: " <b> 2. </b> "
---

## Workshop report

### 2.3. Proposal (Project Proposal)

**Serverless AI Inference Platform**
_Cloud Native solution for automated AI image analysis and diagnosis_

**1. Executive Summary**

The Serverless AI Inference Platform is designed to provide a robust, auto-scalable cloud infrastructure for running heavy Artificial Intelligence (Computer Vision) models. Instead of maintaining costly 24/7 GPU servers, the platform leverages a Serverless Event-Driven architecture on AWS. End users can securely upload images, and the system will automatically queue them and trigger AI analysis, returning near real-time diagnostic results with fully optimized operational costs.

**2. Problem Statement**

- **Current problem:** Deploying AI models (such as YOLO, PyTorch) to production environments is often difficult due to large model sizes and high hardware resource requirements (RAM/CPU). Uploading high-resolution images to traditional servers frequently causes network bottlenecks, and the cost of maintaining an EC2 server during low-traffic periods is wasteful.
- **Solution:** The platform solves the heavy-file upload problem by using **S3 Pre-signed URLs** via API Gateway and Lambda, allowing clients to push images directly to Amazon S3. The new image event triggers **Amazon SQS** as a buffer, which then triggers **AWS Lambda** (running from a Container Image stored on **Amazon ECR**) to perform AI Inference. Text results are stored at high speed in **DynamoDB**, while processed images are saved to a separate S3 bucket. The system has Amazon Cognito built in for API security.
- **Benefits and Return on Investment (ROI):**
  - _Technical:_ Completely eliminates the 10 MB payload limit of API Gateway and the 250 MB code size limit of Lambda. The system scales smoothly when thousands of users upload images simultaneously thanks to SQS-based decoupling.
  - _Financial:_ 100% shift to a Pay-as-you-go model (only charged when AI actually analyzes images). Using **S3 Lifecycle Policy** to automatically move old images to Archive storage (Glacier), saving up to 80% of long-term storage costs compared to traditional systems.

**3. Solution Architecture**

The platform applies an Event-Driven Serverless architecture, ensuring Decoupled independence between the file ingestion flow and the AI processing flow.

![overview](/fcj-workshop-huynhbuyenthanhtoan/images/2-Proposal/diagram1.png)

- **AWS services used:**
  - **Amazon Cognito:** User authentication, JWT token issuance.
  - **Amazon API Gateway & WAF:** Protect the API from attacks and coordinate upload authorization requests.
  - **AWS Lambda (2 functions):**
    - _Presign Lambda:_ Generates an S3 Pre-signed URL granting temporary upload permission.
    - _Inference Lambda:_ Loads the model from ECR, executes image analysis, and outputs results.
  - **Amazon S3 (2 Buckets):** Stores original images (`Raw`) and processed images (`Processed`).
  - **Amazon SQS:** Message queue that receives events from S3 and orchestrates the AI Lambda flow to prevent overload.
  - **Amazon ECR:** Packages AI libraries and Model Weights as a Docker Image (overcoming Lambda's size limitations).
  - **Amazon DynamoDB:** NoSQL database storing diagnostic result parameters with millisecond latency.
  - **CloudWatch & SNS:** Monitor performance (AI runtime, errors) and automatically send email alerts when incidents occur.

**4. Technical Implementation**

- **Phase 1 (Weeks 1–3): Build secure & upload flow.**
  - Configure Cognito User Pool.
  - Write Python Boto3 code for Presign Lambda.
  - Set up API Gateway with Cognito Authorizer integration and test the flow of clients pushing images directly to S3 Raw.
- **Phase 2 (Weeks 4–6): Package AI & Event-driven Pipeline.**
  - Write a Dockerfile packaging PyTorch/YOLO code with model weights. Push to ECR.
  - Set up SQS queue. Configure S3 Event Notification to automatically send messages to SQS when a new image arrives.
  - Create Inference Lambda (based on Container Image), set SQS as Trigger.
- **Phase 3 (Weeks 7–9): Database & Cost Optimization.**
  - Create DynamoDB table to store results.
  - Apply S3 Lifecycle Policy to Raw bucket to automatically archive data after 30 days.
- **Phase 4 (Weeks 10–12): Monitoring & Finalization.**
  - Create CloudWatch Alarms to monitor Lambda `Errors` and `Duration`. Link with SNS to alert via Email. Delete unused resources.

**5. Roadmap & Milestones**

- **Month 1:** Complete Auth infrastructure (Cognito), Storage (S3, DynamoDB), and the API flow for URL issuance.
- **Month 2:** Focus on solving the Core AI challenge: containerize the model with ECR, integrate SQS, and correctly assign IAM Roles for Lambda.
- **Month 3:** End-to-End Testing, optimize Lambda Cold-start time, set up CloudWatch alerting, and perform cleanup.

**6. Budget (Estimated Operational Costs)**

_Estimate for 1,000 image analyses/month (Free Tier & Pay-as-you-go)_

- **Amazon S3:** ~ $0.05 (Image storage and Lifecycle to Glacier).
- **API Gateway + Cognito:** ~ $0.00 (Within Free Tier).
- **AWS Lambda (Inference):** ~ $0.50 (3 GB RAM configuration, ~3 seconds/image).
- **Amazon ECR & SQS:** ~ $0.10 (Image storage and message forwarding fees).
- **Amazon DynamoDB:** ~ $0.00 (Within Free Tier).
- **Total estimated cost:** Under **$1.00/month** — an extremely cost-effective way to operate a heavy AI system compared to renting a fixed VPS/EC2.

**7. Risk Assessment**

- **Risk 1: Lambda Cold-start Timeout.** A large AI model takes a long time to load into RAM on the first run, causing Lambda to time out.
  - _Mitigation:_ Optimize the Base Image size in Docker, increase Lambda RAM and Timeout to 5 minutes, or use Provisioned Concurrency if needed.
- **Risk 2: S3 storage costs growing over time.**
  - _Mitigation:_ S3 Lifecycle Policy is already applied to automatically move old images to Deep Archive storage.
- **Risk 3: Throttling during burst image uploads.**
  - _Mitigation:_ The architecture uses SQS (Message Queue) in front of Lambda as a buffer, ensuring sequential AI processing without dropped requests.
