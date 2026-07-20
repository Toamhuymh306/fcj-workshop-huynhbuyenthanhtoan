---
title: "Tuần 9: Pipeline S3, SQS và Lambda"
date: 2026-06-12
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

### Mục tiêu Tuần 9

- Hoàn thiện pipeline xử lý ảnh theo kiến trúc hướng sự kiện.
- Bảo đảm retry, timeout và xử lý từng ảnh được cấu hình an toàn.

### Các công việc triển khai

| Ngày | Công việc | Minh chứng |
| :--- | :--- | :--- |
| 12/06 | Tạo inference queue, visibility timeout và queue policy giới hạn theo raw bucket/account. | SQS attributes và policy |
| 15/06 | Cấu hình `s3:ObjectCreated:*` từ raw bucket đến SQS. | S3 notification configuration |
| 16/06 | Tạo SQS event source mapping đến Inference Lambda với batch size `1`. | Mapping ở trạng thái `Enabled` |
| 17/06 | Kiểm thử ảnh lá hợp lệ: Lambda ghi kết quả `COMPLETED` và processed image. | DynamoDB item, S3 object, CloudWatch log |
| 18/06 | Kiểm thử ảnh không hợp lệ: Rekognition gate ghi `REJECTED`, không tạo processed image. | DynamoDB item và log kiểm thử |

### Kết quả đạt được

- Luồng S3 → SQS → Lambda → DynamoDB hoạt động đúng với cả nhánh `COMPLETED` và `REJECTED`.
- Queue không còn message tồn đọng sau khi xử lý ổn định.
