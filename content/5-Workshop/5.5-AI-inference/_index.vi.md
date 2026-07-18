---
title: "Triển khai AI inference"
date: 2026-07-18
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Mục tiêu

Phần này đóng gói ResNet-50 và Python runtime thành Lambda container image, push lên Amazon ECR, cấu hình Rekognition validation gate và kết nối SQS với Inference Lambda.

Inference image chỉ chứa một checkpoint production:

```text
best_resnet_model.pth
```

Mô hình nhận ảnh `224 x 224`, chuẩn hóa theo ImageNet và trả một trong 38 lớp PlantVillage. `class_names.json` phải giữ đúng thứ tự class khi train.

#### 1. Build container image

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

Hai tùy chọn `--provenance=false` và `--sbom=false` tạo single-platform manifest phù hợp với Lambda container image.

Kiểm tra checkpoint:

```powershell
docker run --rm `
  --platform linux/amd64 `
  --entrypoint /bin/sh `
  kts-smartagri-inference:local `
  -c "ls -lh /var/task/model"
```

Kết quả chỉ có `best_resnet_model.pth`, `class_names.json` và `model_config.json`.

#### 2. Smoke test model local

```powershell
docker run --rm `
  --platform linux/amd64 `
  --entrypoint /var/lang/bin/python3 `
  -e PYTHONPATH=/var/task `
  -e MODEL_NAME=resnet `
  kts-smartagri-inference:local `
  -c "from runtime import load_assets; a=load_assets(); print(a.model_name, len(a.class_names))"
```

Kỳ vọng:

```text
resnet 38
```

#### 3. Push image lên ECR

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

Kiểm tra `imageManifestMediaType` phải là:

```text
application/vnd.oci.image.manifest.v1+json
```

#### 4. Tạo DynamoDB table

Tạo table `kts-smartagri-dev-inference-results`:

- Partition key: `image_id` kiểu String.
- Billing mode: **On-demand (PAY_PER_REQUEST)**.
- Encryption: AWS owned key hoặc customer managed key theo yêu cầu.

GSI `user_id-index` sẽ được thêm ở phần 5.6.

#### 5. Cấp quyền cho Inference Lambda

Execution role chỉ cần:

- `s3:GetObject` trên raw bucket.
- `s3:PutObject` trên processed bucket.
- `dynamodb:PutItem` trên result table.
- `rekognition:DetectLabels` trên `*`.
- `sqs:ReceiveMessage`, `sqs:DeleteMessage`, `sqs:GetQueueAttributes` trên inference queue.
- Quyền ghi CloudWatch Logs.

{{% notice warning %}}
Không dùng AdministratorAccess hoặc quyền `s3:*`. Lambda lấy owner từ metadata `user-id` đã được ký trong pre-signed URL.
{{% /notice %}}

#### 6. Cấu hình Rekognition Image gate

Trước khi tải ảnh vào PyTorch, Lambda gọi `DetectLabels` trên S3 object:

```python
response = rekognition.detect_labels(
    Image={"S3Object": {"Bucket": bucket, "Name": key}},
    Features=["GENERAL_LABELS"],
    MaxLabels=20,
    MinConfidence=90,
)
```

Quy tắc:

```text
Leaf >= 90% và Plant >= 90%  -> chạy ResNet-50
Thiếu một trong hai nhãn      -> status REJECTED
```

Ảnh bị từ chối không tạo processed image và frontend hiển thị: **Ảnh không hợp lệ, vui lòng chọn ảnh lá cây rõ nét.**

#### 7. Tạo hoặc cập nhật Inference Lambda

| Thuộc tính | Giá trị |
|---|---|
| Function name | `kts-smartagri-dev-inference-lambda` |
| Package type | Image |
| Architecture | x86_64 |
| Memory | 2048 MB |
| Timeout | 120 giây |
| Ephemeral storage | 512 MB |
| `MODEL_NAME` | `resnet` |
| `PROCESSED_BUCKET` | processed bucket |
| `RESULT_TABLE` | DynamoDB table |
| `LEAF_LABEL_MIN_CONFIDENCE` | `90` |

Với function đã tồn tại:

```powershell
aws lambda update-function-code `
  --function-name kts-smartagri-dev-inference-lambda `
  --image-uri $EcrUri `
  --region $AwsRegion

aws lambda wait function-updated-v2 `
  --function-name kts-smartagri-dev-inference-lambda `
  --region $AwsRegion
```

#### 8. Kết nối SQS event source

```powershell
aws lambda create-event-source-mapping `
  --function-name kts-smartagri-dev-inference-lambda `
  --event-source-arn $QueueArn `
  --batch-size 1 `
  --region $AwsRegion
```

Batch size `1` giúp cô lập ảnh lỗi và kiểm soát memory khi mỗi request tải model/image.

#### 9. Xác minh deployment

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

#### Kết quả triển khai

![Kết quả triển khai - docker build success](/images/5-Workshop/5.5-AI-inference/docker-build-success.png)

![Kết quả triển khai - ecr image](/images/5-Workshop/5.5-AI-inference/ecr-image.png)

![Kết quả triển khai - lambda image config](/images/5-Workshop/5.5-AI-inference/lambda-image-config.png)

![Kết quả triển khai - lambda environment](/images/5-Workshop/5.5-AI-inference/lambda-environment.png)

![Kết quả triển khai - lambda permissions](/images/5-Workshop/5.5-AI-inference/lambda-permissions.png)

![Kết quả triển khai - sqs lambda trigger](/images/5-Workshop/5.5-AI-inference/sqs-lambda-trigger.png)

![Kết quả triển khai - rekognition diagram test](/images/5-Workshop/5.5-AI-inference/rekognition-diagram-test.png)

![Kết quả triển khai - rekognition leaf test](/images/5-Workshop/5.5-AI-inference/rekognition-leaf-test.png)
