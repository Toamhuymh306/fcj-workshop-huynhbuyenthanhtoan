---
title: "Deploy AI inference"
date: 2026-07-18
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Objective

This section packages ResNet-50 and its Python runtime as a Lambda container image, pushes it to Amazon ECR, configures the Rekognition validation gate, and connects SQS to Inference Lambda.

The inference image contains one production checkpoint:

```text
best_resnet_model.pth
```

The model accepts `224 x 224` images, applies ImageNet normalization, and outputs one of 38 PlantVillage classes. `class_names.json` must preserve the class order used during training.

#### 1. Build the container image

```powershell
cd D:\kts-smart-agri\ai-service

docker build `
  --platform linux/amd64 `
  --provenance=false `
  --sbom=false `
  -f aws/lambda_inference/Dockerfile `
  -t kts-smartagri-inference:local `
  .
```

`--provenance=false` and `--sbom=false` produce a single-platform manifest compatible with Lambda container images.

Inspect checkpoints:

```powershell
docker run --rm `
  --platform linux/amd64 `
  --entrypoint /bin/sh `
  kts-smartagri-inference:local `
  -c "ls -lh /var/task/model"
```

The image should contain only `best_resnet_model.pth`, `class_names.json`, and `model_config.json`.

#### 2. Smoke-test the model locally

```powershell
docker run --rm `
  --platform linux/amd64 `
  --entrypoint /var/lang/bin/python3 `
  -e PYTHONPATH=/var/task `
  -e MODEL_NAME=resnet `
  kts-smartagri-inference:local `
  -c "from runtime import load_assets; a=load_assets(); print(a.model_name, len(a.class_names))"
```

Expected output:

```text
resnet 38
```

#### 3. Push the image to ECR

```powershell
aws ecr describe-repositories `
  --repository-names $EcrRepository `
  --region $AwsRegion 2>$null | Out-Null

if ($LASTEXITCODE -ne 0) {
  aws ecr create-repository `
    --repository-name $EcrRepository `
    --image-scanning-configuration scanOnPush=true `
    --region $AwsRegion | Out-Null
}

$ImageTag = "resnet-$(Get-Date -Format 'yyyyMMdd-HHmmss')"
$EcrUri = "$AccountId.dkr.ecr.$AwsRegion.amazonaws.com/$EcrRepository`:$ImageTag"

aws ecr get-login-password --region $AwsRegion |
  docker login --username AWS --password-stdin "$AccountId.dkr.ecr.$AwsRegion.amazonaws.com"

docker tag kts-smartagri-inference:local $EcrUri
docker push $EcrUri
```

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

The execution role requires only:

- `s3:GetObject` on the raw bucket.
- `s3:PutObject` on the processed bucket.
- `dynamodb:PutItem` on the result table.
- `rekognition:DetectLabels` on `*`.
- `sqs:ReceiveMessage`, `sqs:DeleteMessage`, and `sqs:GetQueueAttributes` on the inference queue.
- CloudWatch Logs write permissions.

{{% notice warning %}}
Do not use AdministratorAccess or `s3:*`. Lambda obtains the owner from signed `user-id` object metadata.
{{% /notice %}}

#### 6. Configure the Rekognition Image gate

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

For an existing function:

```powershell
aws lambda update-function-code `
  --function-name kts-smartagri-dev-inference-lambda `
  --image-uri $EcrUri `
  --region $AwsRegion

aws lambda wait function-updated-v2 `
  --function-name kts-smartagri-dev-inference-lambda `
  --region $AwsRegion
```

#### 8. Connect the SQS event source

```powershell
aws lambda create-event-source-mapping `
  --function-name kts-smartagri-dev-inference-lambda `
  --event-source-arn $QueueArn `
  --batch-size 1 `
  --region $AwsRegion
```

Batch size `1` isolates failed images and controls memory while each invocation processes model/image data.

#### 9. Verify deployment

```powershell
aws lambda get-function `
  --function-name kts-smartagri-dev-inference-lambda `
  --region $AwsRegion `
  --query "{State:Configuration.State,Update:Configuration.LastUpdateStatus,Architecture:Configuration.Architectures,Image:Code.ResolvedImageUri}"

aws lambda list-event-source-mappings `
  --function-name kts-smartagri-dev-inference-lambda `
  --region $AwsRegion `
  --query "EventSourceMappings[].{State:State,Source:EventSourceArn,BatchSize:BatchSize}"
```

#### Deployment results

![Deployment result - docker build success](/images/5-Workshop/5.5-AI-inference/docker-build-success.png)

![Deployment result - ecr image](/images/5-Workshop/5.5-AI-inference/ecr-image.png)

![Deployment result - lambda image config](/images/5-Workshop/5.5-AI-inference/lambda-image-config.png)

![Deployment result - lambda environment](/images/5-Workshop/5.5-AI-inference/lambda-environment.png)

![Deployment result - lambda permissions](/images/5-Workshop/5.5-AI-inference/lambda-permissions.png)

![Deployment result - sqs lambda trigger](/images/5-Workshop/5.5-AI-inference/sqs-lambda-trigger.png)

![Deployment result - rekognition diagram test](/images/5-Workshop/5.5-AI-inference/rekognition-diagram-test.png)

![Deployment result - rekognition leaf test](/images/5-Workshop/5.5-AI-inference/rekognition-leaf-test.png)
