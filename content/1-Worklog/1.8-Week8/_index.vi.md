---
title: "Tuần 8: MVP End-to-End"
date: 2026-06-05
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

### Mục tiêu Tuần 8:

- Hoàn thiện phiên bản MVP có thể chạy xuyên suốt từ đăng nhập đến xem kết quả.
- Phát hiện lỗi tích hợp sớm trước khi hoàn thiện pipeline sự kiện và bảo mật.

### Các công việc triển khai:

| Thứ | Công việc                                                         | Ngày BĐ    | Ngày HT    | Nguồn tài liệu |
| :-- | :---------------------------------------------------------------- | :--------- | :--------- | :------------- |
| 6   | - Kiểm tra đăng nhập, JWT và API Gateway Authorizer.              | 05/06/2026 | 05/06/2026 | QA Testing     |
| 2   | - Kiểm tra pre-signed URL và upload ảnh trực tiếp lên S3.         | 08/06/2026 | 08/06/2026 | AWS S3         |
| 3   | - Chạy smoke test container và gọi Lambda inference.              | 09/06/2026 | 09/06/2026 | AWS Lambda     |
| 4   | - Kiểm tra lưu/đọc kết quả trong DynamoDB.                        | 10/06/2026 | 10/06/2026 | AWS DynamoDB   |
| 5   | - Ghi nhận lỗi MVP, cập nhật README và kế hoạch hardening.        | 11/06/2026 | 11/06/2026 | GitHub         |

### Kết quả đạt được:

- MVP thực hiện được luồng đăng nhập → upload → inference → lưu/hiển thị kết quả.
- Danh sách lỗi và hạng mục hardening cho tuần 9–11 được xác định rõ.
