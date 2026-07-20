---
title: "Tuần 10: Kiểm thử phân quyền và bảo mật"
date: 2026-06-19
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

### Mục tiêu Tuần 10

- Xác minh dữ liệu lịch sử được cô lập theo Cognito user.
- Rà soát IAM, CORS, validation và thao tác xóa dữ liệu.

### Các công việc triển khai

| Ngày | Công việc | Minh chứng |
| :--- | :--- | :--- |
| 19/06 | Kiểm thử JPEG, PNG, WebP và các input không hợp lệ. | Test matrix và kết quả API |
| 22/06 | Kiểm tra User A không xem được lịch sử của User B. | Unit/integration test |
| 23/06 | Kiểm tra User A không xóa được record hoặc ảnh của User B. | Phản hồi `403` và log |
| 24/06 | Rà soát IAM least privilege, CORS và Cognito Authorizer. | Checklist bảo mật |
| 25/06 | Kiểm tra xóa đúng DynamoDB record, raw image và processed image của chính chủ. | DynamoDB/S3 evidence |

### Kết quả đạt được

- Các test phân quyền người dùng đạt; truy cập chéo bị từ chối.
- Chính sách IAM và CORS chỉ giữ các quyền/origin cần thiết cho đồ án.
