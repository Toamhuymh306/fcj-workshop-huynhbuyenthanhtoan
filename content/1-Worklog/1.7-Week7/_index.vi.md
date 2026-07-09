---
title: "Worklog Tuần 7"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.7. </b> "
---
### Mục tiêu tuần 7:

* Triển khai kiến trúc Event-driven.
* Tách lớp lưu trữ và tính toán bằng Amazon SQS.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu Amazon SQS (Standard vs FIFO) và message patterns | 01/06/2026 | 01/06/2026 | <https://000031.awsstudygroup.com/> |
| 3 | - Tạo Standard SQS Queue cho Inference Pipeline | 02/06/2026 | 02/06/2026 | <https://000032.awsstudygroup.com/> |
| 4 | - Cấu hình S3 Event Notifications gửi s3:ObjectCreated:* về SQS | 03/06/2026 | 03/06/2026 | <https://000033.awsstudygroup.com/> |
| 5 | - Cấu hình SQS Queue làm trigger cho AI Inference Lambda | 04/06/2026 | 04/06/2026 | <https://000034.awsstudygroup.com/> |
| 6 | - Test luồng sự kiện: Upload image -> S3 -> SQS -> Lambda trigger | 05/06/2026 | 05/06/2026 | <https://000035.awsstudygroup.com/> |

### Kết quả đạt được tuần 7:

* Xây dựng thành công pipeline bất đồng bộ ổn định.
* Hệ thống xử lý tốt traffic spike nhờ cơ chế xếp hàng trước khi chạy inference.
