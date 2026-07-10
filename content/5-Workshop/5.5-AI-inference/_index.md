---
title: "AI Inference"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

In this section, we will configure the **AI Inference** service for the **KTs Smart Agriculture** application. After an image is successfully uploaded to Amazon S3, the system performs disease classification using a deep learning model deployed on **AWS Lambda** as a **Container Image**.

The entire inference workflow is fully serverless, enabling automatic scaling, low operational costs, and real-time image analysis.

![AI Inference Architecture](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.5-AI-inference/ai-inference-flow.png)

#### AI Inference Workflow

1. After the user uploads an image to the **Amazon S3 Raw Images Bucket**, the frontend sends an inference request to **Amazon API Gateway**.

2. Amazon API Gateway validates the user's **JWT Access Token** using **Amazon Cognito** to ensure that only authenticated users can access the AI inference service.

![API Gateway](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.5-AI-inference/api-gateway.png)

3. Once authentication succeeds, API Gateway invokes the **Inference Lambda Function**, which is deployed as a **Container Image** stored in **Amazon Elastic Container Registry (Amazon ECR)**.

![Lambda Container](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.5-AI-inference/lambda-container.png)

4. The Lambda function retrieves the uploaded image from Amazon S3 and performs several preprocessing operations before inference:

- Resize the image.
- Normalize pixel values.
- Convert the image into a tensor.
- Load the trained deep learning model.

![Image Preprocessing](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.5-AI-inference/preprocessing.png)

5. The AI model performs inference to classify the plant disease and calculates the prediction confidence.

Example response:

```json
{
  "class": "Tomato___Early_blight",
  "confidence": 0.9876
}
```

![Prediction Result](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.5-AI-inference/prediction.png)

6. The inference result is stored in **Amazon DynamoDB** for future retrieval and analysis.

Each record includes:

- User ID
- Image Name
- Disease Class
- Confidence Score
- Timestamp

![DynamoDB](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.5-AI-inference/dynamodb.png)

7. If the detected disease is classified as severe or the confidence score falls below a predefined threshold, the system publishes a notification using **Amazon Simple Notification Service (Amazon SNS)**.

![Amazon SNS](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.5-AI-inference/sns.png)

8. Finally, the frontend receives the inference response from API Gateway and displays the analysis result to the user.

Example:

```text
Plant        : Tomato

Disease      : Early Blight

Confidence   : 98.76%

Status       : Success
```

![Frontend Result](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.5-AI-inference/frontend-result.png)

---

#### Summary

In this section, we successfully deployed an **AI Inference** service using a fully serverless architecture on AWS.

The inference workflow consists of:

- Authenticating users with Amazon Cognito.
- Receiving inference requests through Amazon API Gateway.
- Executing the AI model inside AWS Lambda (Container Image).
- Loading input images from Amazon S3.
- Storing prediction results in Amazon DynamoDB.
- Sending notifications via Amazon SNS when necessary.

This architecture provides a scalable, cost-efficient, and highly available solution for real-time plant disease classification in the KTs Smart Agriculture platform.
