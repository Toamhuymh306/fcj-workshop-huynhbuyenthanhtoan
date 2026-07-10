---
title: "Tuần 4: Đóng gói Docker & ECR"
date: 2026-06-01
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

### Mục tiêu Tuần 4:

- Đóng gói mô hình học máy thành container độc lập.
- Đẩy image lên kho lưu trữ đám mây của AWS.

### Các công việc triển khai:

| Thứ | Công việc                                               | Ngày BĐ    | Ngày HT    | Nguồn tài liệu |
| :-- | :------------------------------------------------------ | :--------- | :--------- | :------------- |
| 2   | - Viết mã nguồn suy luận (Inference Script).            | 01/06/2026 | 01/06/2026 | Python Docs    |
| 3   | - Thiết lập Dockerfile tối ưu hóa dung lượng (~2.29GB). | 02/06/2026 | 02/06/2026 | Docker Docs    |
| 4   | - Build Docker Image và kiểm thử chạy nội bộ (Local).   | 03/06/2026 | 03/06/2026 | Docker CLI     |
| 5   | - Khởi tạo Repository trên Amazon ECR.                  | 04/06/2026 | 04/06/2026 | AWS ECR Docs   |
| 6   | - Tag và Push Image lên AWS ECR thành công.             | 05/06/2026 | 05/06/2026 | AWS CLI        |

### Kết quả đạt được:

- AI Model được container hóa, loại bỏ hoàn toàn lỗi xung đột môi trường.
- Image sẵn sàng trên Amazon ECR để triển khai cho Lambda.
