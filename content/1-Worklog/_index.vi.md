---
title: "Worklog"
date: 2026-04-17
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

**Trên trang này**, nhóm KTs tổng hợp nhật ký công việc trong quá trình thực tập và triển khai dự án **KTs Smart Agriculture**.

- **Thời gian thực tập được ghi nhận:** 12 tuần, từ **17/04/2026 đến 10/07/2026**.
- **Thời gian chương trình mở rộng:** đến 30/07/2026; giai đoạn 11/07–30/07 không tính vào 12 tuần thực tập chính thức.
- **Mã nguồn đồ án:** [github.com/Toamhuymh306/kts-smart-agri](https://github.com/Toamhuymh306/kts-smart-agri)

Đồ án xây dựng hệ thống nhận diện bệnh cây trồng từ ảnh PlantVillage. Frontend xác thực người dùng bằng Amazon Cognito, nhận pre-signed URL qua API Gateway và Lambda, tải ảnh trực tiếp lên Amazon S3, sau đó S3 gửi sự kiện qua Amazon SQS đến Lambda container chạy mô hình PyTorch/ResNet. Kết quả và lịch sử theo người dùng được lưu trong DynamoDB; CloudWatch hỗ trợ giám sát và xử lý sự cố.

### Lộ trình 12 tuần

**Tuần 1 (17/04–23/04):** [Khởi động chương trình, IAM, AWS CLI và Budget](1.1-week1/)

**Tuần 2 (24/04–30/04):** [Chuẩn bị dữ liệu PlantVillage và yêu cầu AI](1.2-week2/)

**Tuần 3 (01/05–07/05):** [Huấn luyện, đánh giá LeNet/ResNet](1.3-week3/)

**Tuần 4 (08/05–14/05):** [Đóng gói Docker và Amazon ECR](1.4-week4/)

**Tuần 5 (15/05–21/05):** [Serverless backend, S3 và DynamoDB](1.5-week5/)

**Tuần 6 (22/05–28/05):** [API Gateway, Cognito và pre-signed URL](1.6-week6/)

**Tuần 7 (29/05–04/06):** [Frontend và luồng upload ảnh](1.7-week7/)

**Tuần 8 (05/06–11/06):** [MVP end-to-end và kiểm thử tích hợp](1.8-week8/)

**Tuần 9 (12/06–18/06):** [Hoàn thiện pipeline S3 → SQS → Lambda](1.9-week9/)

**Tuần 10 (19/06–25/06):** [Kiểm thử phân quyền và bảo mật](1.10-week10/)

**Tuần 11 (26/06–02/07):** [CloudWatch, tối ưu hiệu năng và chi phí](1.11-week11/)

**Tuần 12 (03/07–10/07):** [Nghiệm thu, tài liệu, demo và bàn giao](1.12-week12/)
