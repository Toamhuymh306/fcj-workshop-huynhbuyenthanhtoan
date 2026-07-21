---
title: "Kết nối S3 với SQS"
date: 2026-07-18
weight: 4
chapter: false
pre: " <b> 5.4.4. </b> "
---

#### 1. Tạo inference queue

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![sqs inference queue](/images/5-Workshop/5.4-S3-upload/sqs-inference-queue.png)


Đặt visibility timeout lớn hơn Lambda timeout. Với Lambda timeout 120 giây, dùng ít nhất 720 giây để đáp ứng khuyến nghị retry của event source mapping.

#### 2. Cấp quyền cho S3 gửi message

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![sqs access policy](/images/5-Workshop/5.4-S3-upload/sqs-access-policy.png)


Queue policy phải cho phép `s3.amazonaws.com` gọi `sqs:SendMessage`, đồng thời ràng buộc:

```text
aws:SourceArn     = arn:aws:s3:::<RAW_BUCKET>
aws:SourceAccount = <ACCOUNT_ID>
```

Không mở `SendMessage` cho principal `*` mà thiếu conditions.

#### 3. Tạo S3 notification

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![s3 event notification](/images/5-Workshop/5.4-S3-upload/s3-event-notification.png)


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

Đặt các giá trị sau theo tài nguyên của bạn:

```bash
RAW_BUCKET=<RAW_BUCKET>
QUEUE_URL=<QUEUE_URL>
AWS_REGION=ap-southeast-1
```

**Bước 1 — Kiểm tra notification của bucket**

```bash
aws s3api get-bucket-notification-configuration \
  --bucket "$RAW_BUCKET" \
  --region "$AWS_REGION"
```

Kết quả phải có một phần tử `QueueConfigurations` với:

- `QueueArn` đúng ARN của inference queue.
- `Events` chứa `s3:ObjectCreated:*`.
- Nếu có filter, prefix phải khớp thư mục upload thực tế, ví dụ `uploads/`.

**Bước 2 — Kiểm tra queue policy và visibility timeout**

```bash
aws sqs get-queue-attributes \
  --queue-url "$QUEUE_URL" \
  --attribute-names QueueArn Policy VisibilityTimeout \
  --region "$AWS_REGION"
```

Xác nhận `VisibilityTimeout` tối thiểu `720`, policy cho phép `s3.amazonaws.com` gọi `sqs:SendMessage`, và có cả `aws:SourceArn` lẫn `aws:SourceAccount`.

**Bước 3 — Gửi một sự kiện thử từ S3**

Sử dụng một ảnh lá nhỏ và một Cognito `sub` dùng cho môi trường dev:

```bash
aws s3 cp ./verification-leaf.jpg \
  "s3://$RAW_BUCKET/uploads/verification-leaf.jpg" \
  --metadata user-id=<COGNITO_SUB> \
  --content-type image/jpeg \
  --region "$AWS_REGION"

sleep 5

aws sqs get-queue-attributes \
  --queue-url "$QUEUE_URL" \
  --attribute-names ApproximateNumberOfMessages ApproximateNumberOfMessagesNotVisible \
  --region "$AWS_REGION"
```

Vì Lambda chưa được nối ở bước này, `ApproximateNumberOfMessages` phải tăng lên sau vài giây. Không dùng nút **Poll for messages** liên tục vì message nhận được sẽ tạm thời bị ẩn theo visibility timeout.

**Tiêu chí hoàn thành 5.4.4**

- S3 notification trỏ đúng inference queue.
- Queue policy chỉ cho phép raw bucket của đúng AWS account gửi message.
- Upload object mới làm số message trong queue tăng.
- Chưa tạo SQS event source mapping; thao tác đó được thực hiện sau khi Lambda ở phần 5.5 sẵn sàng.

Nếu số message không tăng, kiểm tra Region của bucket/queue, ARN trong notification, queue policy và prefix/suffix filter trước khi tiếp tục.
