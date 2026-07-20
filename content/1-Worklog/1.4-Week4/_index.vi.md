---
title: "Tuần 4: Đóng gói Docker & ECR"
date: 2026-05-08
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
| 6   | - Viết mã nguồn suy luận (Inference Script).            | 08/05/2026 | 08/05/2026 | Python Docs    |
| 2   | - Thiết lập Dockerfile cho Lambda container image.      | 11/05/2026 | 11/05/2026 | Docker Docs    |
| 3   | - Build Docker image và kiểm thử chạy nội bộ.           | 12/05/2026 | 12/05/2026 | Docker CLI     |
| 4   | - Khởi tạo repository trên Amazon ECR.                  | 13/05/2026 | 13/05/2026 | AWS ECR Docs   |
| 5   | - Tag, push image và kiểm tra OCI image manifest.       | 14/05/2026 | 14/05/2026 | AWS CLI        |

### Kết quả đạt được:

- AI Model được container hóa, loại bỏ hoàn toàn lỗi xung đột môi trường.
- Image sẵn sàng trên Amazon ECR để triển khai cho Lambda.
