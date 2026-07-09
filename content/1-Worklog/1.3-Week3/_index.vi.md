---
title: "Worklog Tuần 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.3. </b> "
---
### Mục tiêu tuần 3:

* Thiết kế luồng upload file an toàn.
* Triển khai S3 Pre-signed URL để vượt giới hạn payload của API Gateway.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Nghiên cứu giới hạn API Gateway (10 MB) và Lambda (6 MB payload) <br> - Tìm hiểu cơ chế S3 Pre-signed URL | 04/05/2026 | 04/05/2026 | <https://000011.awsstudygroup.com/> |
| 3 | - Viết Lambda (Python/Boto3) tạo pre-signed URL cho put_object | 05/05/2026 | 05/05/2026 | <https://000012.awsstudygroup.com/> |
| 4 | - Gán IAM role cho phép Lambda tạo URL với đúng S3 bucket mục tiêu | 06/05/2026 | 06/05/2026 | <https://000013.awsstudygroup.com/> |
| 5 | - Kết nối Lambda tạo URL vào secured endpoint trên API Gateway | 07/05/2026 | 07/05/2026 | <https://000014.awsstudygroup.com/> |
| 6 | - Test end-to-end luồng upload trực tiếp từ client lên S3 bằng URL tạm thời | 08/05/2026 | 08/05/2026 | <https://000015.awsstudygroup.com/> |

### Kết quả đạt được tuần 3:

* Giải quyết bottleneck upload file lớn bằng kiến trúc direct-to-S3.
* Tạo thành công URL upload tạm thời, an toàn bằng Lambda và Boto3.
