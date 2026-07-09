---
title: "Worklog Tuần 9"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.9. </b> "
---
### Mục tiêu tuần 9:

* Tối ưu chi phí lưu trữ.
* Tự động bảo trì dữ liệu bằng S3 Lifecycle Policies.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Nghiên cứu S3 Storage Classes (Standard, IA, Glacier Flexible Retrieval, Deep Archive) | 15/06/2026 | 15/06/2026 | <https://000041.awsstudygroup.com/> |
| 3 | - Tìm hiểu cách hoạt động của S3 Lifecycle Policies so với Lambda cron jobs | 16/06/2026 | 16/06/2026 | <https://000042.awsstudygroup.com/> |
| 4 | - Tạo bucket s3-archive hoặc định nghĩa prefix để archive | 17/06/2026 | 17/06/2026 | <https://000043.awsstudygroup.com/> |
| 5 | - Cấu hình Lifecycle Rule trên s3-raw-images để chuyển object sang Glacier sau 30 ngày | 18/06/2026 | 18/06/2026 | <https://000044.awsstudygroup.com/> |
| 6 | - Rà soát và xác nhận policy trong tab quản trị S3 | 19/06/2026 | 19/06/2026 | <https://000045.awsstudygroup.com/> |

### Kết quả đạt được tuần 9:

* Thay thế quy trình bảo trì dữ liệu thủ công bằng tính năng native của S3.
* Đảm bảo tối ưu chi phí lưu trữ dài hạn cho nền tảng.
