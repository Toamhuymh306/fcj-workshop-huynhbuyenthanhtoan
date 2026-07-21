---
title: "Triển khai AI inference"
date: 2026-07-18
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

#### Mục tiêu

Phần này đóng gói ResNet-50 và Python runtime thành Lambda container image, push lên Amazon ECR, cấu hình Rekognition validation gate và kết nối SQS với Inference Lambda.

![Architecture Flow](/images/5-Workshop/5.5-AI-inference/diagram4.png)

Inference image chỉ chứa một checkpoint production:

```text
best_resnet_model.pth
```

Mô hình nhận ảnh `224 x 224`, chuẩn hóa theo ImageNet và trả một trong 38 lớp PlantVillage. `class_names.json` phải giữ đúng thứ tự class khi train.

#### 1. Build container image

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![docker build success](/images/5-Workshop/5.5-AI-inference/docker-build-success.png)


Hai tùy chọn `--provenance=false` và `--sbom=false` tạo single-platform manifest phù hợp với Lambda container image.

Kiểm tra checkpoint:

Kết quả chỉ có `best_resnet_model.pth`, `class_names.json` và `model_config.json`.

#### 2. Smoke test model local

Kỳ vọng:

```text
resnet 38
```

#### 3. Push image lên ECR

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![ecr image](/images/5-Workshop/5.5-AI-inference/ecr-image.png)


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

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![lambda permissions](/images/5-Workshop/5.5-AI-inference/lambda-permissions.png)


Execution role chỉ cần:

- `s3:GetObject` trên raw bucket.
- `s3:PutObject` trên processed bucket.
- `dynamodb:PutItem` trên result table.
- `rekognition:DetectLabels` trên `*`.
- `sqs:ReceiveMessage`, `sqs:DeleteMessage`, `sqs:GetQueueAttributes` trên inference queue.
- Quyền ghi CloudWatch Logs.

#### 6. Cấu hình Rekognition Image gate

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![rekognition diagram test](/images/5-Workshop/5.5-AI-inference/rekognition-diagram-test.png)

![rekognition leaf test](/images/5-Workshop/5.5-AI-inference/rekognition-leaf-test.png)


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

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![lambda image config](/images/5-Workshop/5.5-AI-inference/lambda-image-config.png)

![lambda environment](/images/5-Workshop/5.5-AI-inference/lambda-environment.png)


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


#### 8. Kết nối SQS event source

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![sqs lambda trigger](/images/5-Workshop/5.5-AI-inference/sqs-lambda-trigger.png)


Batch size `1` giúp cô lập ảnh lỗi và kiểm soát memory khi mỗi request tải model/image.

#### 9. Xác minh deployment

Đặt các giá trị theo môi trường dev:

```bash
AWS_REGION=ap-southeast-1
FUNCTION_NAME=kts-smartagri-dev-inference-lambda
RAW_BUCKET=<RAW_BUCKET>
PROCESSED_BUCKET=<PROCESSED_BUCKET>
QUEUE_ARN=<QUEUE_ARN>
RESULT_TABLE=kts-smartagri-dev-inference-results
```

**Bước 1 — Kiểm tra cấu hình Lambda**

```bash
aws lambda get-function-configuration \
  --function-name "$FUNCTION_NAME" \
  --region "$AWS_REGION" \
  --query '{State:State,LastUpdateStatus:LastUpdateStatus,PackageType:PackageType,Architectures:Architectures,MemorySize:MemorySize,Timeout:Timeout,Environment:Environment.Variables}'
```

Kết quả đạt khi `State` là `Active`, `LastUpdateStatus` là `Successful`, package type là `Image`, architecture là `x86_64`, timeout là `120` và các biến `MODEL_NAME`, `PROCESSED_BUCKET`, `RESULT_TABLE`, `LEAF_LABEL_MIN_CONFIDENCE` có giá trị đúng.

**Bước 2 — Kiểm tra SQS event source mapping**

```bash
aws lambda list-event-source-mappings \
  --function-name "$FUNCTION_NAME" \
  --event-source-arn "$QUEUE_ARN" \
  --region "$AWS_REGION" \
  --query 'EventSourceMappings[].{State:State,BatchSize:BatchSize,LastProcessingResult:LastProcessingResult}'
```

Phải có đúng mapping cần dùng, `State` là `Enabled` và `BatchSize` là `1`.

**Bước 3 — Kiểm thử end-to-end với ảnh lá hợp lệ**

Đổi `<COGNITO_SUB>` thành `sub` của user dev. UUID ở đầu tên file sẽ trở thành `image_id`, giúp truy vấn kết quả dễ dàng:

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

Sau khi Lambda xử lý xong, item phải có `status = COMPLETED`, `user_id`, `prediction`, `confidence`, `processedImageKey` và `modelName = resnet`. Kiểm tra ảnh kết quả:

```bash
aws s3api head-object \
  --bucket "$PROCESSED_BUCKET" \
  --key "processed/<COGNITO_SUB>/${VALID_ID}.jpg" \
  --region "$AWS_REGION"
```

**Bước 4 — Kiểm thử Rekognition gate với ảnh không phải lá cây**

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

Item phải có `status = REJECTED`, `rejectionReason = NOT_A_LEAF_IMAGE`; processed bucket không được có ảnh cho `INVALID_ID`.

**Bước 5 — Kiểm tra log và queue**

```bash
aws logs tail "/aws/lambda/$FUNCTION_NAME" \
  --since 15m \
  --region "$AWS_REGION"

aws sqs get-queue-attributes \
  --queue-url <QUEUE_URL> \
  --attribute-names ApproximateNumberOfMessages ApproximateNumberOfMessagesNotVisible \
  --region "$AWS_REGION"
```

Log không được có lỗi load checkpoint, thiếu `user-id`, thiếu biến môi trường hoặc timeout. Sau khi xử lý ổn định, số message visible/not-visible phải trở về `0` (các số liệu SQS là gần đúng và có thể cập nhật chậm).
