---
title: "KTS Smart Agri Workshop"
date: 2026-07-18
weight: 1
chapter: false
---

# Build a serverless AI plant disease diagnosis system on AWS

KTS Smart Agri is a hands-on workshop for building a complete serverless web application. Users sign in, upload a plant leaf image to Amazon S3, and receive a disease classification from a ResNet-50 model running on AWS Lambda.

The system uses Amazon Cognito for authentication, API Gateway and Lambda for pre-signed URLs, S3 and SQS for the asynchronous pipeline, Amazon Rekognition Image for input validation, DynamoDB for result history, and CloudWatch for monitoring.

{{% notice info %}}
The workshop is deployed in Region `ap-southeast-1`. Sample resources use the `kts-smartagri-dev` prefix.
{{% /notice %}}

## Architecture

![KTS Smart Agri architecture](/images/5-Workshop/5.1-Workshop-overview/diagram1.png)

## Workshop content

1. [Architecture overview](5-Workshop/5.1-Workshop-overview/)
2. [Prerequisites](5-Workshop/5.2-Prerequisite/)
3. [User authentication with Amazon Cognito](5-Workshop/5.3-Cognito-auth/)
4. [Secure image upload and the S3 - SQS pipeline](5-Workshop/5.4-S3-upload/)
5. [Package and deploy AI inference](5-Workshop/5.5-AI-inference/)
6. [Build result history and data ownership](5-Workshop/5.6-Results-history/)
7. [End-to-end testing and monitoring](5-Workshop/5.7-End-to-end/)
8. [Clean up resources](5-Workshop/5.8-Cleanup/)

## Outcome

After completing the workshop, you will have an end-to-end system with user authentication, direct S3 upload, asynchronous inference, image validation, per-user result history, and APIs protected by a Cognito Authorizer.
