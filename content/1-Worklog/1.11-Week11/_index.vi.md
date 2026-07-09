---
title: "Worklog Tuần 11"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 1.11. </b> "
---
### Mục tiêu tuần 11:

* Triển khai bảo mật, giám sát và cảnh báo.
* Theo dõi sức khỏe hệ thống bằng CloudWatch và SNS.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu Amazon CloudWatch Metrics và Alarms | 29/06/2026 | 29/06/2026 | <https://000051.awsstudygroup.com/> |
| 3 | - Thiết lập Amazon SNS Topic và subscribe email nhận cảnh báo | 30/06/2026 | 30/06/2026 | <https://000052.awsstudygroup.com/> |
| 4 | - Tạo CloudWatch Alarm dựa trên metric Lambda Errors (Threshold > 0) | 01/07/2026 | 01/07/2026 | <https://000053.awsstudygroup.com/> |
| 5 | - Tạo CloudWatch Alarm cho Lambda Duration để bắt rủi ro timeout | 02/07/2026 | 02/07/2026 | <https://000054.awsstudygroup.com/> |
| 6 | - Chủ động tạo lỗi trên Lambda để kiểm tra SNS email alert | 03/07/2026 | 03/07/2026 | <https://000055.awsstudygroup.com/> |

### Kết quả đạt được tuần 11:

* Thiết lập thành công hệ thống giám sát và cảnh báo tự động.
* Đảm bảo team nhận thông báo email kịp thời khi inference fail hoặc suy giảm hiệu năng.
