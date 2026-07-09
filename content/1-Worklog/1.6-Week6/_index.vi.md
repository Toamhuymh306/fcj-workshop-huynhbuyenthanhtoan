---
title: "Worklog Tuần 6"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.6. </b> "
---
### Mục tiêu tuần 6:

* Deploy model AI dạng container lên AWS Lambda.
* Tối ưu cấu hình Lambda cho tác vụ tính toán nặng.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Tạo Lambda function mới, chọn loại Container Image từ ECR | 25/05/2026 | 25/05/2026 | <https://000026.awsstudygroup.com/> |
| 3 | - Cấu hình Lambda: tăng Timeout (5 phút) và Memory (3008 MB) | 26/05/2026 | 26/05/2026 | <https://000027.awsstudygroup.com/> |
| 4 | - Cấp quyền IAM để Lambda đọc S3 Raw Images và ghi S3 Processed Images | 27/05/2026 | 27/05/2026 | <https://000028.awsstudygroup.com/> |
| 5 | - Cập nhật logic Lambda: tải object từ S3, chạy inference, upload kết quả | 28/05/2026 | 28/05/2026 | <https://000029.awsstudygroup.com/> |
| 6 | - Kiểm thử thủ công Inference Lambda trên AWS Console | 29/05/2026 | 29/05/2026 | <https://000030.awsstudygroup.com/> |

### Kết quả đạt được tuần 6:

* Chạy thành công model PyTorch hoàn toàn trong môi trường serverless.
* Xử lý được vấn đề cold start và giới hạn bộ nhớ của Lambda.
