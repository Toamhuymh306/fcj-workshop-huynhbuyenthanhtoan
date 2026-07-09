---
title: "Worklog Tuần 5"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.5. </b> "
---
### Mục tiêu tuần 5:

* Container hóa ứng dụng AI để vượt giới hạn kích thước deploy của Lambda.
* Làm chủ Amazon Elastic Container Registry (ECR).

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Học Docker cơ bản và nguyên lý container hóa <br> - Cài Docker local | 18/05/2026 | 18/05/2026 | <https://000021.awsstudygroup.com/> |
| 3 | - Viết Dockerfile từ base image public.ecr.aws/lambda/python <br> - Đóng gói PyTorch, OpenCV và model weights | 19/05/2026 | 19/05/2026 | <https://000022.awsstudygroup.com/> |
| 4 | - Build và test Docker image local bằng AWS Lambda Runtime Interface Emulator | 20/05/2026 | 20/05/2026 | <https://000023.awsstudygroup.com/> |
| 5 | - Tạo private repository trên Amazon ECR | 21/05/2026 | 21/05/2026 | <https://000024.awsstudygroup.com/> |
| 6 | - Authenticate Docker CLI tới ECR và push AI container image | 22/05/2026 | 22/05/2026 | <https://000025.awsstudygroup.com/> |

### Kết quả đạt được tuần 5:

* Container hóa thành công workload ML nặng.
* Push thành công image lên Amazon ECR để sẵn sàng chạy serverless.
