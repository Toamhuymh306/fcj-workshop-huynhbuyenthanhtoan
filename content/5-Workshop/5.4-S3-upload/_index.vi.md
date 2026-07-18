---
title: "Tải ảnh an toàn và pipeline sự kiện"
date: 2026-07-18
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Mục tiêu

Frontend không gửi file ảnh qua Lambda hoặc API Gateway. Thay vào đó, Presign Lambda tạo URL có thời hạn để trình duyệt upload trực tiếp lên raw S3 bucket. Metadata `user-id` được ký trong URL và trở thành nguồn xác định chủ sở hữu xuyên suốt pipeline.

Sau khi upload, S3 gửi event đến SQS. Queue hấp thụ burst, giữ message khi Lambda lỗi và tách upload khỏi thời gian inference.

#### Ba lớp lưu trữ

| Bucket | Mục đích |
|---|---|
| `kts-smartagri-dev-raw-images` | Ảnh gốc do người dùng tải lên |
| `kts-smartagri-dev-processed-images` | Ảnh JPEG đã gắn nhãn dự đoán |
| `kts-smartagri-dev-archive-images` | Dữ liệu lưu trữ hoặc kiểm thử quản trị |

#### Nội dung

1. [Tạo bucket, Presign Lambda và API](5.4.1-api-gateway-setup/)
2. [Cấu hình CORS](5.4.2-cors-config/)
3. [Kiểm thử upload](5.4.3-test-endpoint/)
4. [Kết nối S3 với SQS](5.4.4-event-pipeline/)
