---
title: "Prerequisites"
date: 2026-07-18
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Required tools

- An AWS account allowed to create Cognito, S3, SQS, ECR, Lambda, DynamoDB, API Gateway, Rekognition, and IAM roles.
- AWS CLI v2 with configured credentials.
- Docker Desktop using Linux containers on `linux/amd64`.
- Python 3.11 or later for local tests.
- KTS Smart Agri source code and `ai-service/best_resnet_model.pth`.
- Hugo for local workshop preview.

{{% notice warning %}}
Do not use the AWS root user. Use a dedicated IAM identity and remove temporary credentials after the workshop.
{{% /notice %}}

#### 1. Verify AWS CLI

```powershell
aws --version
aws configure list
aws sts get-caller-identity
aws configure get region
```

Expected Region:

```text
ap-southeast-1
```

#### 2. Verify Docker Desktop

```powershell
docker version
docker info --format "OSType={{.OSType}} Architecture={{.Architecture}}"
docker context show
```

The output must include `OSType=linux`, `Architecture=x86_64`, and the `desktop-linux` context.

#### 3. Verify model and source files

```powershell
cd D:\kts-smart-agri\ai-service

Test-Path .\best_resnet_model.pth
Test-Path .\aws\lambda_inference\Dockerfile
Test-Path .\aws\lambda_inference\runtime.py
Test-Path ..\frontend-app\backend\lambda\presign_handler.py
Test-Path ..\frontend-app\backend\lambda\results_handler.py
```

Every command must return `True`.

#### 4. Define workshop variables

```powershell
$AwsRegion = "ap-southeast-1"
$AccountId = aws sts get-caller-identity --query Account --output text
$Project = "kts-smartagri"
$Environment = "dev"

$RawBucket = "$Project-$Environment-raw-images"
$ProcessedBucket = "$Project-$Environment-processed-images"
$ArchiveBucket = "$Project-$Environment-archive-images"
$ResultTable = "$Project-$Environment-inference-results"
$InferenceQueue = "$Project-$Environment-inference-queue"
$EcrRepository = "$Project-inference"
```

S3 bucket names are globally unique. Add your account ID or another suffix if a name is unavailable.

#### 5. Verify the production checkpoint

The workshop packages one checkpoint:

```text
best_resnet_model.pth
```

The image also requires `runtime.py`, `handler.py`, `class_names.json`, `model_config.json`, PyTorch, Torchvision, and Pillow. A checkpoint contains weights, not the complete inference pipeline.

#### Verification complete

Continue after Docker, the AWS CLI, AWS access, and the required AI files have all been verified successfully.
