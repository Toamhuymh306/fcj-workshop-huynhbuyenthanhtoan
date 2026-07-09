---
title: "Worklog Tuần 10"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.10. </b> "
---
### Mục tiêu tuần 10:

* Tích hợp hệ thống và kiểm thử end-to-end.
* Xác thực toàn bộ luồng kiến trúc đã thiết kế.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Mapping toàn bộ flow Client -> Cognito -> API Gateway -> Lambda -> S3 -> SQS -> ECR Lambda -> DB | 22/06/2026 | 22/06/2026 | <https://000046.awsstudygroup.com/> |
| 3 | - Chạy nhiều vòng E2E test mô phỏng người dùng thực tế | 23/06/2026 | 23/06/2026 | <https://000047.awsstudygroup.com/> |
| 4 | - Debug edge cases (ảnh lỗi định dạng, JWT hết hạn) | 24/06/2026 | 24/06/2026 | <https://000048.awsstudygroup.com/> |
| 5 | - Rà CloudWatch Logs để phát hiện warning/inefficiency ẩn | 25/06/2026 | 25/06/2026 | <https://000049.awsstudygroup.com/> |
| 6 | - Tinh chỉnh IAM Roles theo Principle of Least Privilege | 26/06/2026 | 26/06/2026 | <https://000050.awsstudygroup.com/> |

### Kết quả đạt được tuần 10:

* Toàn bộ pipeline Serverless AI hoạt động ổn định, an toàn và có khả năng chịu tải.
* Xác nhận luồng dữ liệu chạy đúng với sơ đồ kiến trúc Draw.io đã thiết kế.
