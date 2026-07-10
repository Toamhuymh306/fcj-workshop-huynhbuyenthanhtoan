---
title: "Workshop"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Deploying a Serverless AI System for Crop Disease Analysis

#### Overview

This system applies artificial intelligence to diagnose crop diseases, utilizing a 100% Serverless architecture on AWS.

In this lab, we will learn how to configure and deploy the system in practice: from user authentication and secure image upload to the cloud, to triggering AI inference, and storing the results.

This architecture provides two core benefits:

- **Security & Upload Optimization** - Uses Amazon Cognito for Tokens and API Gateway for Pre-signed URLs, allowing secure, direct image uploads to Amazon S3.
- **Automated AI Inference** - An Event-Driven mechanism uses S3 triggers to invoke AWS Lambda, running the analysis model as soon as new data arrives.

#### Contents

1. [Workshop overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequisite/)
3. [User Authentication (Cognito)](5.3-Cognito-auth/)
4. [Secure Image Upload (Pre-signed URL & S3)](5.4-S3-upload/)
5. [Automated AI Inference (Lambda & ECR)](5.5-AI-inference/)
6. [Storing Results (DynamoDB)](5.6-Dynamodb-results/)
