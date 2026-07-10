---
title: "Tuần 6: API Gateway & Bảo mật"
date: 2026-06-15
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

### Mục tiêu Tuần 6:

- Mở cổng API giao tiếp với Web App an toàn.
- Tích hợp xác thực để chặn người dùng trái phép.

### Các công việc triển khai:

| Thứ | Công việc                                               | Ngày BĐ    | Ngày HT    | Nguồn tài liệu |
| :-- | :------------------------------------------------------ | :--------- | :--------- | :------------- |
| 2   | - Tạo REST API trên Amazon API Gateway.                 | 15/06/2026 | 15/06/2026 | API Gateway    |
| 3   | - Code Lambda function sinh Pre-signed URL cho S3.      | 16/06/2026 | 16/06/2026 | Boto3 Docs     |
| 4   | - Liên kết API endpoint `/presign` với Lambda function. | 17/06/2026 | 17/06/2026 | AWS API        |
| 5   | - Cấu hình Cross-Origin Resource Sharing (CORS).        | 18/06/2026 | 18/06/2026 | Web Security   |
| 6   | - Tích hợp Cognito Authorizer yêu cầu JWT Token.        | 19/06/2026 | 19/06/2026 | AWS Cognito    |

### Kết quả đạt được:

- Cơ chế Upload an toàn: Client nhận thẻ ủy quyền tạm thời (Pre-signed URL) để tải file trực tiếp lên S3.
- Chặn đứng hoàn toàn truy cập `401 Unauthorized` từ bên ngoài.
