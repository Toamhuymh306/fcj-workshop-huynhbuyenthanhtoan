---
title: "Worklog Tuần 8"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.8. </b> "
---
### Mục tiêu tuần 8:

* Lưu trữ kết quả inference bằng cơ sở dữ liệu NoSQL.
* Triển khai Amazon DynamoDB.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tìm hiểu DynamoDB cơ bản (Table, Partition Key, Sort Key) | 08/06/2026 | 08/06/2026 | <https://000036.awsstudygroup.com/> |
| 3 | - Tạo bảng AI_Diagnosis_Results với Partition Key là ImageID | 09/06/2026 | 09/06/2026 | <https://000037.awsstudygroup.com/> |
| 4 | - Gán IAM policy cho Inference Lambda với quyền dynamodb:PutItem | 10/06/2026 | 10/06/2026 | <https://000038.awsstudygroup.com/> |
| 5 | - Cập nhật mã Python Lambda để parse kết quả inference và ghi vào DynamoDB | 11/06/2026 | 11/06/2026 | <https://000039.awsstudygroup.com/> |
| 6 | - Kiểm tra dữ liệu lưu trên DynamoDB Console sau khi xử lý ảnh | 12/06/2026 | 12/06/2026 | <https://000040.awsstudygroup.com/> |

### Kết quả đạt được tuần 8:

* Tích hợp thành công cơ sở dữ liệu NoSQL có khả năng mở rộng cao.
* Hệ thống lưu ổn định metadata và kết quả chẩn đoán song song với ảnh đã xử lý.
