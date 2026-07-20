---
title: "Tuần 11: Giám sát và tối ưu"
date: 2026-06-26
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

### Mục tiêu Tuần 11

- Tăng khả năng quan sát và xử lý sự cố của hệ thống.
- Tối ưu Lambda container, lưu trữ và chi phí vận hành.

### Các công việc triển khai

| Ngày | Công việc | Minh chứng |
| :--- | :--- | :--- |
| 26/06 | Chuẩn hóa CloudWatch Logs cho presign, inference và results Lambda. | Log groups và log mẫu |
| 29/06 | Theo dõi duration, error, throttle, queue depth và age of oldest message. | Metrics/dashboard |
| 30/06 | Đo cold start, inference time và mức sử dụng memory. | Báo cáo hiệu năng |
| 01/07 | Tối ưu container, memory/timeout, S3 lifecycle và DynamoDB TTL. | Cấu hình trước/sau |
| 02/07 | Hoàn thiện runbook xử lý timeout, DLQ, CORS và lỗi model. | Troubleshooting guide |

### Kết quả đạt được

- Có đủ log/metric để truy vết một lần upload ảnh từ đầu đến cuối.
- Cấu hình chi phí và hiệu năng được ghi nhận, có thể audit và lặp lại.
