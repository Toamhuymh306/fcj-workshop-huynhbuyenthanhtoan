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
