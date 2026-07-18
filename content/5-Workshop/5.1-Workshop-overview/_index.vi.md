---
title: "Tổng quan kiến trúc"
date: 2026-07-18
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

#### Kiến trúc tổng thể

![Kiến trúc KTS Smart Agri](/images/5-Workshop/5.1-Workshop-overview/diagram1.png)

Luồng chính của hệ thống:

1. Người dùng đăng ký, xác minh email và nhận ID token từ Amazon Cognito.
2. Frontend gọi `POST /presign` qua API Gateway với ID token.
3. Presign Lambda tạo URL tải lên có thời hạn và ràng buộc metadata `user-id`.
4. Trình duyệt tải ảnh trực tiếp lên raw S3 bucket.
5. S3 gửi `ObjectCreated` event đến SQS; SQS kích hoạt Inference Lambda.
6. Inference Lambda gọi Rekognition `DetectLabels` để kiểm tra `Leaf` và `Plant`.
7. Ảnh hợp lệ được ResNet-50 xử lý; ảnh không hợp lệ nhận trạng thái `REJECTED`.
8. Lambda lưu ảnh đã xử lý vào S3 và kết quả vào DynamoDB.
9. Frontend gọi Results API để lấy hoặc xóa lịch sử của chính người dùng.

#### Tại sao dùng kiến trúc này?

| Yêu cầu | Thiết kế |
|---|---|
| Không truyền file lớn qua API Gateway | Upload trực tiếp lên S3 bằng pre-signed URL |
| Tách upload khỏi inference | S3 + SQS tạo pipeline bất đồng bộ |
| Chạy PyTorch và checkpoint lớn | Lambda container image lưu trên ECR |
| Chặn ảnh người, động vật, tài liệu | Rekognition Image làm validation gate |
| Cô lập dữ liệu từng người dùng | Cognito `sub`, DynamoDB GSI và kiểm tra ownership |
| Giảm vận hành máy chủ | Dịch vụ managed/serverless, tính phí theo sử dụng |

#### Các dịch vụ AWS

- **Amazon Cognito:** đăng ký, xác minh email và phát hành JWT.
- **Amazon API Gateway:** công khai Presign API và Results API.
- **AWS Lambda:** cấp URL, inference và quản lý lịch sử.
- **Amazon S3:** lưu ảnh raw, processed và archive.
- **Amazon SQS:** đệm sự kiện upload, retry và tách tải.
- **Amazon ECR:** lưu Lambda container image.
- **Amazon Rekognition Image:** xác nhận ảnh có lá cây.
- **Amazon DynamoDB:** lưu kết quả và truy vấn theo `user_id`.
- **Amazon CloudWatch:** log, metric và điều tra lỗi.

{{% notice tip %}}
Không đưa access key, secret key, JWT hoặc pre-signed URL còn hiệu lực vào ảnh chụp workshop.
{{% /notice %}}
