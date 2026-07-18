---
title: "Dọn dẹp tài nguyên"
date: 2026-07-18
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

#### Trước khi xóa

{{% notice danger %}}
Không thực hiện phần này trên môi trường đang phục vụ người dùng. Sao lưu ảnh và kết quả cần giữ trước khi xóa bucket hoặc DynamoDB table.
{{% /notice %}}

Xóa theo thứ tự phụ thuộc để tránh lỗi resource in use.

#### 1. Tắt pipeline

1. Disable rồi delete SQS event source mapping của Inference Lambda.
2. Xóa S3 event notification trên raw bucket.
3. Chờ SQS queue không còn message cần xử lý.

#### 2. Xóa API và Lambda

1. Xóa API Gateway stage/deployment hoặc toàn bộ REST API nếu là API riêng cho lab.
2. Xóa permission cho API Gateway invoke Lambda.
3. Xóa Results Lambda, Presign Lambda và Inference Lambda.
4. Xóa các CloudWatch log group nếu không cần audit.

#### 3. Xóa dữ liệu

```powershell
aws s3 rm "s3://$RawBucket" --recursive --region $AwsRegion
aws s3 rm "s3://$ProcessedBucket" --recursive --region $AwsRegion
aws s3 rm "s3://$ArchiveBucket" --recursive --region $AwsRegion

aws s3api delete-bucket --bucket $RawBucket --region $AwsRegion
aws s3api delete-bucket --bucket $ProcessedBucket --region $AwsRegion
aws s3api delete-bucket --bucket $ArchiveBucket --region $AwsRegion

aws dynamodb delete-table --table-name $ResultTable --region $AwsRegion
aws sqs delete-queue --queue-url $QueueUrl --region $AwsRegion
```

#### 4. Xóa image và repository

```powershell
aws ecr delete-repository `
  --repository-name $EcrRepository `
  --force `
  --region $AwsRegion
```

#### 5. Xóa Cognito và IAM

1. Xóa Cognito app client rồi xóa User Pool.
2. Xóa inline policies khỏi ba Lambda execution roles.
3. Detach managed policies.
4. Xóa roles sau cùng.

#### 6. Xác minh chi phí

Kiểm tra AWS Billing/Cost Explorer sau 24 giờ:

- Lambda không còn invocation.
- S3 không còn storage.
- ECR không còn image storage.
- Rekognition không còn `DetectLabels` calls.
- DynamoDB table và SQS queue đã bị xóa.

#### Kết quả triển khai

![Kết quả triển khai - cleanup empty buckets](/images/5-Workshop/5.8-Cleanup/cleanup-empty-buckets.png)

![Kết quả triển khai - cleanup resources removed](/images/5-Workshop/5.8-Cleanup/cleanup-resources-removed.png)

![Kết quả triển khai - cleanup cost check](/images/5-Workshop/5.8-Cleanup/cleanup-cost-check.png)
