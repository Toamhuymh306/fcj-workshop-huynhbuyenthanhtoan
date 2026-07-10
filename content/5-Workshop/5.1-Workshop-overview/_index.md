---
title: "Introduction"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Serverless Architecture

- **Serverless architecture** allows you to build and run applications without managing server infrastructure. These services scale automatically, offer built-in redundancy, and ensure high availability.
- The crop disease analysis platform utilizes a combination of core AWS Serverless services: **Amazon Cognito** for identity management, **API Gateway** as the communication hub, **Amazon S3** for image storage, **AWS Lambda** (via ECR containers) to execute the AI inference model, and **DynamoDB** to store the output results.

#### Workshop overview

In this workshop, the deployment is divided into two primary communicating environments:

- **"Client-side / Frontend"** represents the Web App (built with Vanilla HTML/JS) running on the user's browser. This application makes API calls to authenticate and push images directly to the cloud.
- **"Cloud-side / Backend"** is the underlying AWS ecosystem. When a leaf image is successfully uploaded, S3 automatically triggers a Lambda function containing the deep learning model. Lambda processes the image, classifies the disease, and writes the results to DynamoDB for the Frontend to retrieve and display to the user.

![overview](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.1-Workshop-overview/diagram1.png)
