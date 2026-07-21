---
title: "Các bước chuẩn bị"
date: 2026-07-18
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

#### Công cụ cần thiết

- Tài khoản AWS có quyền tạo Cognito, S3, SQS, ECR, Lambda, DynamoDB, API Gateway, Rekognition và IAM role.
- AWS CLI v2 đã cấu hình credentials.
- Docker Desktop chạy Linux containers, kiến trúc `linux/amd64`.
- Python 3.11 hoặc mới hơn để kiểm thử local.
- Source code KTS Smart Agri và checkpoint `ai-service/best_resnet_model.pth`.
- Hugo để xem workshop local.

#### 1. Kiểm tra AWS CLI

```powershell
aws --version
aws configure list
aws sts get-caller-identity
aws configure get region
```

Region kỳ vọng:

```text
ap-southeast-1
```

#### 2. Kiểm tra Docker Desktop

```powershell
docker version
docker info --format "OSType={{.OSType}} Architecture={{.Architecture}}"
docker context show
```

Kết quả cần có `OSType=linux`, `Architecture=x86_64` và context `desktop-linux`.

#### 3. Kiểm tra model và source code

```powershell
cd D:\kts-smart-agri\ai-service

Test-Path .\best_resnet_model.pth
Test-Path .\aws\lambda_inference\Dockerfile
Test-Path .\aws\lambda_inference\runtime.py
Test-Path ..\frontend-app\backend\lambda\presign_handler.py
Test-Path ..\frontend-app\backend\lambda\results_handler.py
```

Tất cả lệnh phải trả về `True`.

#### 4. Khai báo biến dùng xuyên suốt workshop

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

S3 bucket name phải duy nhất toàn cầu. Nếu tên đã tồn tại, thêm account ID hoặc hậu tố riêng.

#### 5. Kiểm tra checkpoint production

Workshop chỉ đóng gói một checkpoint:

```text
best_resnet_model.pth
```

Image vẫn cần `runtime.py`, `handler.py`, `class_names.json`, `model_config.json`, PyTorch, Torchvision và Pillow. Checkpoint chỉ chứa trọng số, không chứa toàn bộ pipeline inference.

#### Hoàn tất kiểm tra

Tiếp tục khi Docker, AWS CLI, quyền truy cập AWS và các file AI đều đã được xác minh thành công.
