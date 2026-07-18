---
title: "Workshop"
date: 2026-07-18
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Xây dựng hệ thống AI Serverless chẩn đoán bệnh lá cây trên AWS

#### Tổng quan

Trong workshop này, bạn sẽ triển khai **KTS Smart Agri**, một ứng dụng web serverless cho phép người dùng đăng nhập, tải ảnh lá cây và nhận kết quả chẩn đoán từ mô hình ResNet-50.

Hệ thống sử dụng Amazon Cognito để xác thực, API Gateway và Lambda để cấp pre-signed URL, Amazon S3 và SQS cho pipeline hướng sự kiện, Amazon Rekognition Image để loại ảnh không hợp lệ, Lambda container để chạy mô hình, DynamoDB để lưu lịch sử và CloudWatch để giám sát.

{{% notice info %}}
Workshop sử dụng Region `ap-southeast-1`. Tên tài nguyên trong hướng dẫn có hậu tố `dev`; hãy thay bằng tên duy nhất của bạn khi triển khai trong tài khoản khác.
{{% /notice %}}

#### Kết quả đạt được

- Người dùng đăng ký và xác minh email qua Cognito.
- Ảnh được tải trực tiếp từ trình duyệt lên S3 bằng pre-signed URL.
- S3 gửi sự kiện qua SQS để kích hoạt inference bất đồng bộ.
- Rekognition yêu cầu đồng thời nhãn `Leaf` và `Plant` trước khi chạy AI.
- ResNet-50 phân loại 38 lớp bệnh/lá và ghi kết quả vào DynamoDB.
- API lịch sử chỉ trả về và xóa dữ liệu thuộc người dùng đang đăng nhập.

#### Nội dung

1. [Tổng quan kiến trúc](5.1-Workshop-overview/)
2. [Điều kiện tiên quyết](5.2-Prerequisite/)
3. [Xác thực người dùng với Cognito](5.3-Cognito-auth/)
4. [Tải ảnh an toàn và pipeline sự kiện](5.4-S3-upload/)
5. [Triển khai AI inference](5.5-AI-inference/)
6. [Lịch sử kết quả và phân quyền dữ liệu](5.6-Results-history/)
7. [Kiểm thử end-to-end và giám sát](5.7-End-to-end/)
8. [Dọn dẹp tài nguyên](5.8-Cleanup/)
