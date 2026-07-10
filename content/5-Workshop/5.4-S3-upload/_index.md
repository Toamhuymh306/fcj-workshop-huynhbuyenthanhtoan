---
title: "Architecture: Serverless Image Upload"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.0 </b> "
---

#### Secure Image Ingestion Flow

This section breaks down the serverless architecture used to securely ingest raw images into the system, setting the stage for the downstream computer vision inference pipeline. By utilizing a Pre-signed URL approach, the architecture minimizes backend load and optimizes upload latency.

**Step-by-step Execution:**

1. **User Authentication:** The client authenticates against **Amazon Cognito** to obtain a valid JSON Web Token (JWT), ensuring only verified users can interact with the system.
2. **Edge Routing & Protection:** The user requests an upload URL via HTTPS. This request is routed globally through **Amazon CloudFront**, which is shielded against web exploits by **AWS WAF**.
3. **Authorization & API Management:** **Amazon API Gateway** intercepts the request and validates the provided JWT token directly with Cognito.
4. **Pre-signed URL Generation:** Once authorized, the API Gateway triggers the `AWS Lambda Presign` function. This compute layer securely communicates with Amazon S3 to generate a temporary Pre-signed URL.
5. **Direct S3 Upload:** The Pre-signed URL is returned to the frontend (Steps 5a & 5b). The client application then uses this URL to upload the image directly to the **Raw Images S3 Bucket** (Step 6), completely bypassing the application backend.

This decoupled ingestion mechanism ensures that raw data is safely stored and immediately ready to trigger the AI processing events.

![Architecture Flow](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-upload/diagram1_2.png)
