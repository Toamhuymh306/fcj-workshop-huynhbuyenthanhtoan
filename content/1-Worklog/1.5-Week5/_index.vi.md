---
title: "Tuần 5: Serverless Backend"
date: 2026-06-08
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
| 2   | - Khởi tạo Lambda function từ ECR Image chứa mô hình.           | 08/06/2026 | 08/06/2026 | AWS Lambda     |
| 3   | - Thiết lập IAM Role cấp quyền truy cập S3 và DynamoDB.         | 09/06/2026 | 09/06/2026 | AWS IAM        |
| 4   | - Tạo bảng `kts-smartagri-dev-inference-results` trên DynamoDB. | 10/06/2026 | 10/06/2026 | AWS DynamoDB   |
| 5   | - Cấu hình S3 Bucket (`kts-smartagri-dev-raw-images`).          | 11/06/2026 | 11/06/2026 | AWS S3         |
| 6   | - Thiết lập Event Trigger kích hoạt Lambda khi có ảnh mới.      | 12/06/2026 | 12/06/2026 | AWS Events     |

### Kết quả đạt được:

- Pipeline tự động: Ảnh đưa lên S3 -> Kích hoạt Lambda -> Trả kết quả về DynamoDB hoạt động trơn tru.
