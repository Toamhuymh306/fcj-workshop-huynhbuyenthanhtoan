---
title: "Workshop"
date: 2026-07-18
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Building a Serverless AI Plant Disease Diagnosis System on AWS

#### Overview

In this workshop, you will deploy **KTS Smart Agri**, a serverless web application where authenticated users upload leaf images and receive diagnoses from a ResNet-50 model.

The system uses Amazon Cognito for authentication, API Gateway and Lambda for pre-signed URLs, Amazon S3 and SQS for an event-driven pipeline, Amazon Rekognition Image to reject invalid uploads, a Lambda container for model inference, DynamoDB for scan history, and CloudWatch for observability.

{{% notice info %}}
The workshop uses Region `ap-southeast-1`. Resource names use a `dev` suffix; replace them with globally unique names when deploying in another account.
{{% /notice %}}

#### Outcomes

- Users register and verify their email through Cognito.
- Browsers upload images directly to S3 with a pre-signed URL.
- S3 sends events through SQS for asynchronous inference.
- Rekognition requires both `Leaf` and `Plant` labels before AI inference.
- ResNet-50 classifies 38 plant disease/health classes and stores results in DynamoDB.
- The history API only reads and deletes records owned by the authenticated user.

#### Contents

1. [Architecture overview](5.1-Workshop-overview/)
2. [Preparation steps](5.2-Prerequisite/)
3. [User authentication with Cognito](5.3-Cognito-auth/)
4. [Secure upload and event pipeline](5.4-S3-upload/)
5. [Deploy AI inference](5.5-AI-inference/)
6. [Results history and data authorization](5.6-Results-history/)
7. [End-to-end testing and monitoring](5.7-End-to-end/)
8. [Clean up resources](5.8-Cleanup/)
