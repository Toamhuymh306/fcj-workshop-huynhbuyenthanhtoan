---
title: "End-to-end testing and monitoring"
date: 2026-07-18
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

#### Preparation

1. Run the frontend at `http://127.0.0.1:8000`.
2. Sign in with an email-verified Cognito account.
3. Open DevTools Network to observe `/presign`, the S3 upload, and `/results`.
4. Do not expose JWTs or pre-signed URLs in screenshots.

#### Test 1: Valid leaf image

Upload an image from the PlantVillage validation set.

Expected results:

- Presign API returns `200`.
- The raw S3 object contains `user-id` metadata.
- The SQS message is processed without a persistent backlog.
- Rekognition returns `Leaf` and `Plant` at or above 90%.
- DynamoDB contains `status=COMPLETED`, prediction, confidence, and `modelName=resnet`.
- The processed bucket contains a JPEG with `Content-Type=image/jpeg`.
- The frontend displays the crop, disease, and confidence.

#### Test 2: Invalid image

Upload a diagram, person, or animal image.

Expected results:

- Rekognition does not return both `Leaf` and `Plant`.
- DynamoDB contains `status=REJECTED` and `rejectionReason=NOT_A_LEAF_IMAGE`.
- No processed image is created.
- The frontend displays **Invalid image. Please select a clear leaf image.**
- ResNet does not run for this image.

#### Test 3: Results API and CORS

```powershell
curl.exe -i -X OPTIONS "$ApiBaseUrl/results" `
  -H "Origin: http://127.0.0.1:8000" `
  -H "Access-Control-Request-Method: GET" `
  -H "Access-Control-Request-Headers: Authorization,Content-Type"
```

Without a token, `GET /results` must return `401` with CORS headers. With a valid ID token, it returns `200` and only that user's history.

#### CloudWatch Logs

Inspect these log groups:

```text
/aws/lambda/kts-smartagri-dev-presign-lambda
/aws/lambda/kts-smartagri-dev-inference-lambda
/aws/lambda/kts-smartagri-dev-results-lambda
```

Inference logs should include:

- Model load duration and checkpoint path.
- Rekognition validation result.
- Inference duration for valid images.
- No JWTs, pre-signed URLs, or image bytes.

#### Metrics to monitor

- Lambda `Errors`, `Duration`, `Throttles`, and `ConcurrentExecutions`.
- SQS `ApproximateNumberOfMessagesVisible` and age of oldest message.
- API Gateway `4XXError`, `5XXError`, and `Latency`.
- DynamoDB throttled requests.

#### Deployment results

![Deployment result - valid leaf result](/images/5-Workshop/5.7-End-to-end/valid-leaf-result.png)

![Deployment result - invalid image result](/images/5-Workshop/5.7-End-to-end/invalid-image-result.png)

![Deployment result - scan history](/images/5-Workshop/5.7-End-to-end/scan-history.png)

![Deployment result - processed object](/images/5-Workshop/5.7-End-to-end/processed-object.png)

![Deployment result - cloudwatch inference log](/images/5-Workshop/5.7-End-to-end/cloudwatch-inference-log.png)

![Deployment result - cloudwatch metrics](/images/5-Workshop/5.7-End-to-end/cloudwatch-metrics.png)
#### Completion criteria

The workshop is complete when all three tests pass, no unexpected messages remain in the queue, and user A's records never appear in user B's session.
