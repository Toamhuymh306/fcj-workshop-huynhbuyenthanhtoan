---
title: "Giới thiệu"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Kiến trúc Serverless (Không máy chủ)

- **Kiến trúc Serverless** cho phép xây dựng và chạy các ứng dụng mà không cần phải quản lý cơ sở hạ tầng máy chủ. Các dịch vụ này tự động mở rộng quy mô, có tính dự phòng cao và đảm bảo tính sẵn sàng.
- Nền tảng phân tích bệnh cây trồng sử dụng sự kết hợp của các dịch vụ Serverless cốt lõi trên AWS: **Amazon Cognito** để quản lý danh tính, **API Gateway** làm cổng giao tiếp, **Amazon S3** để lưu trữ hình ảnh, **AWS Lambda** (kết hợp ECR container) để chạy mô hình suy luận AI, và **DynamoDB** để lưu trữ kết quả đầu ra.

#### Tổng quan workshop

Trong workshop này, quá trình triển khai được chia làm 2 môi trường giao tiếp chính:

- **"Client-side / Frontend"** đại diện cho ứng dụng Web App (viết bằng HTML/JS thuần) chạy trên trình duyệt của người dùng. Ứng dụng này sẽ gọi API để xác thực và đẩy ảnh trực tiếp lên đám mây.
- **"Cloud-side / Backend"** là hệ sinh thái AWS hoạt động ngầm. Khi một hình ảnh lá cây được tải lên thành công, S3 sẽ tự động kích hoạt (trigger) hàm Lambda chứa mô hình học sâu. Lambda xử lý ảnh, phân loại lớp bệnh và ghi kết quả vào DynamoDB để Frontend lấy dữ liệu hiển thị về cho người dùng.

![overview](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.1-Workshop-overview/diagram1.png)
