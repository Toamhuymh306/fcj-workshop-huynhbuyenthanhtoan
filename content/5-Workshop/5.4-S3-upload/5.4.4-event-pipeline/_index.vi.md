---
title: "Kết nối S3 với SQS"
date: 2026-07-18
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### 1. Tạo inference queue

```powershell
$QueueUrl = aws sqs create-queue `
  --queue-name $InferenceQueue `
  --region $AwsRegion `
  --query QueueUrl `
  --output text

$QueueArn = aws sqs get-queue-attributes `
  --queue-url $QueueUrl `
  --attribute-names QueueArn `
  --region $AwsRegion `
  --query Attributes.QueueArn `
  --output text
```

Đặt visibility timeout lớn hơn Lambda timeout. Với Lambda timeout 120 giây, dùng ít nhất 720 giây để đáp ứng khuyến nghị retry của event source mapping.

#### 2. Cấp quyền cho S3 gửi message

Queue policy phải cho phép `s3.amazonaws.com` gọi `sqs:SendMessage`, đồng thời ràng buộc:

```text
aws:SourceArn     = arn:aws:s3:::<RAW_BUCKET>
aws:SourceAccount = <ACCOUNT_ID>
```

Không mở `SendMessage` cho principal `*` mà thiếu conditions.

#### 3. Tạo S3 notification

Raw bucket gửi mọi sự kiện `s3:ObjectCreated:*` đến queue:

```json
{
  "QueueConfigurations": [
    {
      "Id": "TriggerSQSInference",
      "QueueArn": "<QUEUE_ARN>",
      "Events": ["s3:ObjectCreated:*"]
    }
  ]
}
```

Chỉ cấu hình event source mapping từ SQS đến Lambda sau khi Inference Lambda được tạo ở phần 5.5.

#### 4. Xác minh

```powershell
aws s3api get-bucket-notification-configuration `
  --bucket $RawBucket `
  --region $AwsRegion
```

#### Kết quả triển khai

![Kết quả triển khai - sqs inference queue](/images/5-Workshop/5.4-S3-upload/sqs-inference-queue.png)

![Kết quả triển khai - sqs access policy](/images/5-Workshop/5.4-S3-upload/sqs-access-policy.png)

![Kết quả triển khai - s3 event notification](/images/5-Workshop/5.4-S3-upload/s3-event-notification.png)
