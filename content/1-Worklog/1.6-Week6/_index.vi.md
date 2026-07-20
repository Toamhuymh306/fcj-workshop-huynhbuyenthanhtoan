---
title: "Tuần 6: API Gateway & Bảo mật"
date: 2026-05-22
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
| 6   | - Tạo REST API trên Amazon API Gateway.                 | 22/05/2026 | 22/05/2026 | API Gateway    |
| 2   | - Hoàn thiện Lambda function sinh pre-signed URL.       | 25/05/2026 | 25/05/2026 | Boto3 Docs     |
| 3   | - Liên kết endpoint `/presign` và `/results`.           | 26/05/2026 | 26/05/2026 | AWS API        |
| 4   | - Cấu hình CORS và kiểm thử preflight.                  | 27/05/2026 | 27/05/2026 | Web Security   |
| 5   | - Tích hợp Cognito Authorizer và kiểm tra JWT claims.   | 28/05/2026 | 28/05/2026 | AWS Cognito    |

### Kết quả đạt được:

- Cơ chế Upload an toàn: Client nhận thẻ ủy quyền tạm thời (Pre-signed URL) để tải file trực tiếp lên S3.
- Request không có hoặc có token sai bị từ chối với `401 Unauthorized`.
