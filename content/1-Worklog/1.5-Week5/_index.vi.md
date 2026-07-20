---
title: "Tuần 5: Serverless Backend"
date: 2026-05-15
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

### Mục tiêu Tuần 5:

- Xây dựng luồng xử lý không máy chủ (Serverless) tự động phân tích ảnh.
- Thiết lập cơ sở dữ liệu NoSQL lưu trữ kết quả.

### Các công việc triển khai:

| Thứ | Công việc                                                       | Ngày BĐ    | Ngày HT    | Nguồn tài liệu |
| :-- | :-------------------------------------------------------------- | :--------- | :--------- | :------------- |
| 6   | - Thiết kế Lambda functions và luồng backend serverless.        | 15/05/2026 | 15/05/2026 | AWS Lambda     |
| 2   | - Thiết lập IAM Role tối thiểu cho S3 và DynamoDB.              | 18/05/2026 | 18/05/2026 | AWS IAM        |
| 3   | - Tạo bảng `kts-smartagri-dev-inference-results` trên DynamoDB. | 19/05/2026 | 19/05/2026 | AWS DynamoDB   |
| 4   | - Tạo raw/processed S3 buckets, CORS và lifecycle.               | 20/05/2026 | 20/05/2026 | AWS S3         |
| 5   | - Viết và kiểm thử presign/results Lambda handlers.             | 21/05/2026 | 21/05/2026 | Boto3/Unittest |

### Kết quả đạt được:

- Backend cơ bản tạo pre-signed URL và lưu/truy vấn kết quả trên DynamoDB; pipeline sự kiện được hoàn thiện ở tuần 9.
