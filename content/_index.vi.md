---
title: "KTS Smart Agri Workshop"
date: 2026-07-18
weight: 1
chapter: false
---

# Xây dựng hệ thống AI Serverless chẩn đoán bệnh lá cây trên AWS

KTS Smart Agri là workshop hướng dẫn xây dựng một ứng dụng web serverless hoàn chỉnh: người dùng đăng nhập, tải ảnh lá cây lên Amazon S3 và nhận kết quả phân loại bệnh từ mô hình ResNet-50 chạy trên AWS Lambda.

Hệ thống sử dụng Amazon Cognito để xác thực, API Gateway và Lambda để cấp pre-signed URL, S3 và SQS cho pipeline bất đồng bộ, Amazon Rekognition Image để loại ảnh không hợp lệ, DynamoDB để lưu lịch sử và CloudWatch để giám sát.

{{% notice info %}}
Workshop được triển khai tại Region `ap-southeast-1`. Các tên tài nguyên mẫu dùng tiền tố `kts-smartagri-dev`.
{{% /notice %}}

## Kiến trúc

![Kiến trúc KTS Smart Agri](/images/5-Workshop/5.1-Workshop-overview/diagram1.png)

## Nội dung workshop

1. [Tổng quan kiến trúc](5-Workshop/5.1-Workshop-overview/)
2. [Các bước chuẩn bị](5-Workshop/5.2-Prerequisite/)
3. [Xác thực người dùng với Amazon Cognito](5-Workshop/5.3-Cognito-auth/)
4. [Tải ảnh an toàn và xây dựng pipeline S3 - SQS](5-Workshop/5.4-S3-upload/)
5. [Đóng gói và triển khai AI inference](5-Workshop/5.5-AI-inference/)
6. [Xây dựng lịch sử kết quả và phân quyền dữ liệu](5-Workshop/5.6-Results-history/)
7. [Kiểm thử end-to-end và giám sát](5-Workshop/5.7-End-to-end/)
8. [Dọn dẹp tài nguyên](5-Workshop/5.8-Cleanup/)

## Kết quả đạt được

Sau workshop, bạn có một hệ thống hoạt động end-to-end với xác thực người dùng, upload trực tiếp lên S3, inference bất đồng bộ, kiểm tra ảnh đầu vào, lưu lịch sử theo từng người dùng và API được bảo vệ bằng Cognito Authorizer.
