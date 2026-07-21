---
title: "Deploy AI inference"
date: 2026-07-18
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Objective

This section packages ResNet-50 and its Python runtime as a Lambda container image, pushes it to Amazon ECR, configures the Rekognition validation gate, and connects SQS to Inference Lambda.

![Architecture Flow](/images/5-Workshop/5.5-AI-inference/diagram4.png)

The inference image contains one production checkpoint:

```text
best_resnet_model.pth
```

The model accepts `224 x 224` images, applies ImageNet normalization, and outputs one of 38 PlantVillage classes. `class_names.json` must preserve the class order used during training.

#### 1. Build the container image

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![docker build success](/images/5-Workshop/5.5-AI-inference/docker-build-success.png)


`--provenance=false` and `--sbom=false` produce a single-platform manifest compatible with Lambda container images.

Inspect checkpoints:

The image should contain only `best_resnet_model.pth`, `class_names.json`, and `model_config.json`.

#### 2. Smoke-test the model locally

Expected output:

```text
resnet 38
```

#### 3. Push the image to ECR

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![ecr image](/images/5-Workshop/5.5-AI-inference/ecr-image.png)


Verify that `imageManifestMediaType` is:

```text
application/vnd.oci.image.manifest.v1+json
```

#### 4. Create the DynamoDB table

Create `kts-smartagri-dev-inference-results` with:

- String partition key: `image_id`.
- Billing mode: **On-demand (PAY_PER_REQUEST)**.
- AWS owned key or a customer managed encryption key as required.

Add the `user_id-index` GSI in section 5.6.

#### 5. Grant Inference Lambda permissions

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![lambda permissions](/images/5-Workshop/5.5-AI-inference/lambda-permissions.png)


The execution role requires only:

- `s3:GetObject` on the raw bucket.
- `s3:PutObject` on the processed bucket.
- `dynamodb:PutItem` on the result table.
- `rekognition:DetectLabels` on `*`.
- `sqs:ReceiveMessage`, `sqs:DeleteMessage`, and `sqs:GetQueueAttributes` on the inference queue.
- CloudWatch Logs write permissions.

#### 6. Configure the Rekognition Image gate

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![rekognition diagram test](/images/5-Workshop/5.5-AI-inference/rekognition-diagram-test.png)

![rekognition leaf test](/images/5-Workshop/5.5-AI-inference/rekognition-leaf-test.png)


Before loading the image into PyTorch, Lambda calls `DetectLabels` for the S3 object:

```python
response = rekognition.detect_labels(
    Image={"S3Object": {"Bucket": bucket, "Name": key}},
    Features=["GENERAL_LABELS"],
    MaxLabels=20,
    MinConfidence=90,
)
```

Validation rule:

```text
Leaf >= 90% and Plant >= 90%  -> run ResNet-50
Either label is missing        -> status REJECTED
```

Rejected images do not create a processed image. The frontend displays: **Invalid image. Please select a clear leaf image.**

#### 7. Create or update Inference Lambda

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![lambda image config](/images/5-Workshop/5.5-AI-inference/lambda-image-config.png)

![lambda environment](/images/5-Workshop/5.5-AI-inference/lambda-environment.png)


| Property | Value |
|---|---|
| Function name | `kts-smartagri-dev-inference-lambda` |
| Package type | Image |
| Architecture | x86_64 |
| Memory | 2048 MB |
| Timeout | 120 seconds |
| Ephemeral storage | 512 MB |
| `MODEL_NAME` | `resnet` |
| `PROCESSED_BUCKET` | processed bucket |
| `RESULT_TABLE` | DynamoDB table |
| `LEAF_LABEL_MIN_CONFIDENCE` | `90` |


#### 8. Connect the SQS event source

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![sqs lambda trigger](/images/5-Workshop/5.5-AI-inference/sqs-lambda-trigger.png)


Batch size `1` isolates failed images and controls memory while each invocation processes model/image data.

#### 9. Verify deployment

Set the values for the development environment:

```bash
AWS_REGION=ap-southeast-1
FUNCTION_NAME=kts-smartagri-dev-inference-lambda
RAW_BUCKET=<RAW_BUCKET>
PROCESSED_BUCKET=<PROCESSED_BUCKET>
QUEUE_ARN=<QUEUE_ARN>
RESULT_TABLE=kts-smartagri-dev-inference-results
```

**Step 1 — Verify the Lambda configuration**

```bash
aws lambda get-function-configuration \
  --function-name "$FUNCTION_NAME" \
  --region "$AWS_REGION" \
  --query '{State:State,LastUpdateStatus:LastUpdateStatus,PackageType:PackageType,Architectures:Architectures,MemorySize:MemorySize,Timeout:Timeout,Environment:Environment.Variables}'
```

Pass criteria: `State` is `Active`, `LastUpdateStatus` is `Successful`, package type is `Image`, architecture is `x86_64`, timeout is `120`, and `MODEL_NAME`, `PROCESSED_BUCKET`, `RESULT_TABLE`, and `LEAF_LABEL_MIN_CONFIDENCE` contain the expected values.

**Step 2 — Verify the SQS event source mapping**

```bash
aws lambda list-event-source-mappings \
  --function-name "$FUNCTION_NAME" \
  --event-source-arn "$QUEUE_ARN" \
  --region "$AWS_REGION" \
  --query 'EventSourceMappings[].{State:State,BatchSize:BatchSize,LastProcessingResult:LastProcessingResult}'
```

The required mapping must exist with `State` set to `Enabled` and `BatchSize` set to `1`.

**Step 3 — Run an end-to-end test with a valid leaf image**

Replace `<COGNITO_SUB>` with the development user's Cognito `sub`. The UUID prefix becomes the `image_id`, making the result deterministic to query:

```bash
VALID_ID=11111111-1111-4111-8111-111111111111

aws s3 cp ./valid-leaf.jpg \
  "s3://$RAW_BUCKET/uploads/${VALID_ID}_leaf.jpg" \
  --metadata user-id=<COGNITO_SUB> \
  --content-type image/jpeg \
  --region "$AWS_REGION"

sleep 10

aws dynamodb get-item \
  --table-name "$RESULT_TABLE" \
  --key "{\"image_id\":{\"S\":\"$VALID_ID\"}}" \
  --consistent-read \
  --region "$AWS_REGION"
```

After processing, the item must include `status = COMPLETED`, `user_id`, `prediction`, `confidence`, `processedImageKey`, and `modelName = resnet`. Verify the processed image:

```bash
aws s3api head-object \
  --bucket "$PROCESSED_BUCKET" \
  --key "processed/<COGNITO_SUB>/${VALID_ID}.jpg" \
  --region "$AWS_REGION"
```

**Step 4 — Test the Rekognition gate with a non-leaf image**

```bash
INVALID_ID=22222222-2222-4222-8222-222222222222

aws s3 cp ./non-leaf.jpg \
  "s3://$RAW_BUCKET/uploads/${INVALID_ID}_non-leaf.jpg" \
  --metadata user-id=<COGNITO_SUB> \
  --content-type image/jpeg \
  --region "$AWS_REGION"

sleep 10

aws dynamodb get-item \
  --table-name "$RESULT_TABLE" \
  --key "{\"image_id\":{\"S\":\"$INVALID_ID\"}}" \
  --consistent-read \
  --region "$AWS_REGION"
```

The item must contain `status = REJECTED` and `rejectionReason = NOT_A_LEAF_IMAGE`; the processed bucket must not contain an image for `INVALID_ID`.

**Step 5 — Inspect logs and queue state**

```bash
aws logs tail "/aws/lambda/$FUNCTION_NAME" \
  --since 15m \
  --region "$AWS_REGION"

aws sqs get-queue-attributes \
  --queue-url <QUEUE_URL> \
  --attribute-names ApproximateNumberOfMessages ApproximateNumberOfMessagesNotVisible \
  --region "$AWS_REGION"
```

Logs must not contain checkpoint-loading failures, missing `user-id` metadata, missing environment variables, or timeouts. After stable processing, both message counts should return to `0` (SQS counts are approximate and can update with a delay).
