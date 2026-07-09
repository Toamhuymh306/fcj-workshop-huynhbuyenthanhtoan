---
title: "Worklog Tuần 4"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 1.4. </b> "
---
### Mục tiêu tuần 4:

* Chuẩn bị AI model cho nền tảng inference.
* Tối ưu model Computer Vision để phù hợp triển khai serverless.

### Các công việc cần triển khai trong tuần này:
| Thứ | Công việc | Ngày bắt đầu | Ngày hoàn thành | Nguồn tài liệu |
| --- | --- | --- | --- | --- |
| 2 | - Rà soát các model Plant Disease CNN/YOLO hiện có <br> - Tập trung bộ dữ liệu nhận diện nhện đỏ trên lá cacao | 11/05/2026 | 11/05/2026 | <https://000016.awsstudygroup.com/> |
| 3 | - Tinh chỉnh mã tiền xử lý ảnh bằng Python/OpenCV | 12/05/2026 | 12/05/2026 | <https://000017.awsstudygroup.com/> |
| 4 | - Export model PyTorch sang định dạng .pt hoặc .onnx đã tối ưu | 13/05/2026 | 13/05/2026 | <https://000018.awsstudygroup.com/> |
| 5 | - Viết script inference để load model và xử lý ảnh local | 14/05/2026 | 14/05/2026 | <https://000019.awsstudygroup.com/> |
| 6 | - Chạy test local để đo thời gian inference và mức tiêu thụ RAM | 15/05/2026 | 15/05/2026 | <https://000020.awsstudygroup.com/> |

### Kết quả đạt được tuần 4:

* Tối ưu được trọng số model và pipeline preprocessing.
* Xác lập baseline về bộ nhớ và thời gian thực thi cho giai đoạn triển khai AWS.
