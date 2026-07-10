---
title: "Workshop"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

# Triển khai hệ thống AI Serverless phân tích bệnh cây trồng

#### Tổng quan

Hệ thống ứng dụng trí tuệ nhân tạo để chẩn đoán bệnh trên cây trồng, sử dụng kiến trúc hoàn toàn không máy chủ (Serverless) trên AWS.

Trong bài lab này, chúng ta sẽ học cách cấu hình và triển khai thực tế hệ thống: từ bước xác thực người dùng, tải ảnh an toàn lên đám mây, kích hoạt suy luận AI (Inference), cho đến việc lưu trữ và hiển thị kết quả.

Kiến trúc này mang đến hai lợi ích cốt lõi:

- **Bảo mật & Tối ưu luồng tải lên** - Sử dụng Amazon Cognito cấp Token và API Gateway tạo Pre-signed URL, cho phép tải ảnh trực tiếp lên Amazon S3 một cách an toàn.
- **Tự động hóa AI Inference** - Cơ chế hướng sự kiện (Event-Driven) dùng S3 trigger kích hoạt AWS Lambda chạy mô hình phân tích ngay khi có dữ liệu mới.

#### Nội dung

1. [Tổng quan về workshop](5.1-Workshop-overview/)
2. [Chuẩn bị](5.2-Prerequisite/)
3. [Xác thực người dùng (Cognito)](5.3-Cognito-auth/)
4. [Luồng tải ảnh an toàn (Pre-signed URL & S3)](5.4-S3-upload/)
5. [Tự động hóa suy luận AI (Lambda & ECR)](5.5-AI-inference/)
6. [Lưu trữ kết quả (DynamoDB)](5.6-Dynamodb-results/)
7. [Dọn dẹp tài nguyên](5.7-Cleanup/)
