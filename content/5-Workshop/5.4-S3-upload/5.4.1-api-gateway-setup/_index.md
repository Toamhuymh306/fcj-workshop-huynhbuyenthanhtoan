---
title: "API Gateway & Lambda Setup"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

To prepare for the secure image upload flow, the KTs Smart Agriculture system will not push image data directly through a web server. Instead, we will implement the **Pre-signed URL** mechanism (Temporary authorization link).

This mechanism requires two main components:

- **AWS Lambda Function:** Responsible for communicating with Amazon S3 to generate a time-limited `PUT` URL.
- **Amazon API Gateway:** Acts as the communication gateway for the Frontend to invoke the Lambda function.

![Pre-signed URL Flow](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-upload/presign-flow.png)

#### Step 1: Create the URL Generation Lambda Function

1. Navigate to the [AWS Lambda Console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1) and click **Create function**.
2. Name the function `kts-smartagri-presign-handler`, and select **Python 3.10** (or newer) as the Runtime.
3. Ensure the Lambda's Execution Role has a Policy attached granting `s3:PutObject` permission to the `kts-smartagri-dev-raw-images` bucket.
4. Write the Python code using the `boto3` library to generate the URL with the `generate_presigned_url` method. Click **Deploy** to save the code.

![Lambda Setup](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-upload/lambda-presign.png)

#### Step 2: Configure Amazon API Gateway

Instead of having the Frontend call Lambda directly, we use API Gateway to manage traffic and security (integrating Cognito in later steps).

1. Navigate to the **Amazon API Gateway Console** and choose to build a new **REST API**.
2. In the API management interface, click **Create Resource** and set the path name to `/presign`.

![Create Resource](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-upload/api-resource.png)

3. Select the newly created `/presign` resource, click **Create Method**, and choose the **POST** method.
4. For the Integration type, select **Lambda Function** and type in the function name `kts-smartagri-presign-handler` created in Step 1. Click Save.

![Lambda Integration](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-upload/api-integration.png)

#### Step 3: Deploy the API

1. Click the **Deploy API** button on the toolbar.
2. Create a new Stage named `dev` and proceed with the deployment.
3. Once deployed, the system will provide you with an **Invoke URL**. Copy this URL as you will need it to configure the `config.js` file in the Frontend application.

![Get Invoke URL](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-upload/api-deploy.png)
