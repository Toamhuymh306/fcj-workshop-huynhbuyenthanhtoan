---
title: "Tuần 3: Huấn luyện mô hình AI"
date: 2026-05-01
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

### Mục tiêu Tuần 3:

- Thiết kế kiến trúc Mạng nơ-ron tích chập (CNN).
- Huấn luyện mô hình và đánh giá hiệu năng nhận diện bệnh.

### Các công việc triển khai:

| Thứ | Công việc                                                 | Ngày BĐ    | Ngày HT    | Nguồn tài liệu    |
| :-- | :-------------------------------------------------------- | :--------- | :--------- | :---------------- |
| 6   | - Xây dựng và kiểm tra kiến trúc LeNet bằng PyTorch.       | 01/05/2026 | 01/05/2026 | PyTorch Docs      |
| 2   | - Chuẩn bị ResNet-50, loss function và optimizer.          | 04/05/2026 | 04/05/2026 | PyTorch Guide     |
| 3   | - Huấn luyện LeNet và ResNet trên cùng tập dữ liệu.        | 05/05/2026 | 05/05/2026 | Local/Kaggle      |
| 4   | - Tinh chỉnh siêu tham số và theo dõi overfitting.         | 06/05/2026 | 06/05/2026 | AI Papers         |
| 5   | - So sánh Accuracy, F1-score, kích thước và inference time. | 07/05/2026 | 07/05/2026 | Scikit-learn      |

### Kết quả đạt được:

- Mô hình PyTorch phân loại 38 lớp bệnh cây trồng hoạt động ổn định với độ chính xác cao.
- File trọng số mô hình (`.pt`/`.pth`) đã sẵn sàng để tích hợp.
