---
title: "Bản đề xuất"
date: 2024-01-01
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

## Báo cáo workshop

### 2.3. Proposal (Đề xuất dự án)

**Serverless AI Inference Platform**
_Giải pháp Cloud Native tự động hóa phân tích và chẩn đoán hình ảnh AI_

**1. Tóm tắt điều hành**

Serverless AI Inference Platform được thiết kế nhằm cung cấp một hạ tầng đám mây mạnh mẽ, có khả năng mở rộng tự động để chạy các mô hình Trí tuệ Nhân tạo (Computer Vision) hạng nặng. Thay vì phải duy trì các máy chủ GPU đắt đỏ 24/7, nền tảng này ứng dụng kiến trúc Serverless Event-Driven trên AWS. Người dùng cuối có thể tải hình ảnh lên một cách bảo mật, sau đó hệ thống sẽ tự động đưa vào hàng đợi và kích hoạt AI phân tích, trả về kết quả chẩn đoán gần như theo thời gian thực với chi phí vận hành được tối ưu hóa ở mức tối đa.

**2. Tuyên bố vấn đề**

- **Vấn đề hiện tại:** Việc triển khai các mô hình AI (như YOLO, PyTorch) lên môi trường thực tế (Production) thường gặp khó khăn do dung lượng mô hình lớn và yêu cầu tài nguyên phần cứng (RAM/CPU) cao. Quá trình người dùng tải ảnh độ phân giải cao lên server truyền thống thường gây nghẽn mạng (bottleneck) và chi phí duy trì server (EC2) khi không có lượng truy cập là rất lãng phí.
- **Giải pháp:** Nền tảng giải quyết bài toán tải file nặng bằng cách sử dụng **S3 Pre-signed URL** qua API Gateway và Lambda, cho phép Client đẩy ảnh trực tiếp lên Amazon S3. Sự kiện ảnh mới sẽ kích hoạt **Amazon SQS** làm bộ đệm (buffer), từ đó trigger **AWS Lambda** (chạy bằng Container Image lưu trên **Amazon ECR**) để thực hiện AI Inference. Kết quả text được lưu siêu tốc vào **DynamoDB**, trong khi ảnh đã xử lý được lưu vào một bucket S3 khác. Hệ thống tích hợp sẵn Amazon Cognito để bảo mật API.
- **Lợi ích và hoàn vốn đầu tư (ROI):**
- _Kỹ thuật:_ Loại bỏ hoàn toàn giới hạn payload 10MB của API Gateway và giới hạn 250MB dung lượng code của Lambda. Hệ thống tự động scale (co giãn) mượt mà khi có hàng ngàn user tải ảnh cùng lúc nhờ cơ chế decoupling bằng SQS.
- _Tài chính:_ Chuyển đổi 100% sang mô hình Pay-as-you-go (chỉ trả tiền khi AI thực sự phân tích ảnh). Sử dụng tính năng **S3 Lifecycle Policy** tự động đẩy ảnh cũ sang lớp lưu trữ Archive (Glacier), giúp tiết kiệm đến 80% chi phí lưu trữ dài hạn so với hệ thống cũ.

**3. Kiến trúc giải pháp**

Nền tảng áp dụng kiến trúc Event-Driven Serverless, đảm bảo tính Decoupled (phân tách độc lập) giữa luồng tiếp nhận file và luồng xử lý AI. (Tham khảo sơ đồ kiến trúc _aws1.drawio_4.png_).

- **Dịch vụ AWS sử dụng:**
- **Amazon Cognito:** Xác thực người dùng, cấp phát JWT token.
- **Amazon API Gateway & WAF:** Bảo vệ API khỏi các cuộc tấn công và điều phối request xin cấp quyền upload.
- **AWS Lambda (2 functions):**
- _Presign Lambda:_ Khởi tạo S3 Pre-signed URL cấp quyền upload tạm thời.
- _Inference Lambda:_ Tải model từ ECR, thực thi phân tích ảnh và xuất kết quả.
- **Amazon S3 (2 Buckets):** Lưu trữ ảnh gốc (`Raw`) và ảnh đã nhận diện (`Processed`).
- **Amazon SQS:** Hàng đợi tin nhắn nhận sự kiện từ S3, điều phối luồng chạy của AI Lambda để chống quá tải.
- **Amazon ECR:** Đóng gói thư viện AI và Model Weights thành Docker Image (giúp vượt rào cản dung lượng của Lambda).
- **Amazon DynamoDB:** Cơ sở dữ liệu NoSQL lưu trữ thông số kết quả chẩn đoán với độ trễ mili-giây.
- **CloudWatch & SNS:** Giám sát hiệu suất (thời gian chạy AI, lỗi) và tự động gửi email cảnh báo khi có sự cố.

**4. Triển khai kỹ thuật**

- **Giai đoạn 1 (Tuần 1-3): Xây dựng luồng bảo mật & Upload.**
- Cấu hình Cognito User Pool.
- Viết code Python Boto3 cho Presign Lambda.
- Thiết lập API Gateway tích hợp Cognito Authorizer và kiểm thử luồng Client đẩy ảnh trực tiếp vào S3 Raw.
- **Giai đoạn 2 (Tuần 4-6): Đóng gói AI & Event-driven Pipeline.**
- Viết Dockerfile đóng gói code PyTorch/YOLO cùng model weights. Push lên ECR.
- Thiết lập hàng đợi SQS. Cấu hình S3 Event Notification tự động bắn message vào SQS khi có ảnh mới.
- Tạo Inference Lambda (dựa trên Container Image), thiết lập SQS làm Trigger.
- **Giai đoạn 3 (Tuần 7-9): Database & Tối ưu chi phí.**
- Tạo bảng DynamoDB lưu kết quả.
- Áp dụng S3 Lifecycle Policy cho bucket Raw để tự động Archive data sau 30 ngày.
- **Giai đoạn 4 (Tuần 10-12): Giám sát & Hoàn thiện.**
- Tạo CloudWatch Alarms theo dõi `Errors` và `Duration` của Lambda. Liên kết với SNS để cảnh báo qua Email. Xóa các tài nguyên thừa.

**5. Lộ trình & Mốc triển khai**

- **Tháng 1:** Hoàn thiện hạ tầng Auth (Cognito), Storage (S3, DynamoDB) và luồng API cấp phát URL.
- **Tháng 2:** Tập trung giải quyết bài toán Core AI: Container hóa mô hình bằng ECR, tích hợp SQS và cấp quyền IAM Role chuẩn xác cho Lambda.
- **Tháng 3:** Kiểm thử toàn trình (End-to-End Test), tối ưu hóa thời gian Cold-start của Lambda, thiết lập hệ thống cảnh báo CloudWatch và dọn dẹp (Clean-up).

**6. Ngân sách (Chi phí vận hành dự kiến)**

_Ước tính cho 1.000 lượt phân tích ảnh/tháng (Free Tier & Pay-as-you-go)_

- **Amazon S3:** ~ 0.05 USD (Lưu trữ ảnh và Lifecycle xuống Glacier).
- **API Gateway + Cognito:** ~ 0.00 USD (Nằm trong Free Tier).
- **AWS Lambda (Inference):** ~ 0.50 USD (Cấu hình 3GB RAM, chạy ~3 giây/ảnh).
- **Amazon ECR & SQS:** ~ 0.10 USD (Phí lưu trữ image và chuyển tiếp message).
- **Amazon DynamoDB:** ~ 0.00 USD (Nằm trong Free Tier).
- **Tổng chi phí ước tính:** Chưa tới **1.00 USD/tháng**, một mức phí cực kỳ ấn tượng để vận hành một hệ thống AI hạng nặng so với việc thuê VPS/EC2 cố định.

**7. Đánh giá rủi ro**

- **Rủi ro 1: Lambda Cold-start Timeout.** Mô hình AI lớn mất nhiều thời gian tải vào RAM ở lần chạy đầu tiên khiến Lambda bị timeout.
- _Giảm thiểu:_ Tối ưu hóa dung lượng Base Image trong Docker, tăng cấu hình RAM và Timeout của Lambda lên mức 5 phút, hoặc sử dụng Provisioned Concurrency nếu cần thiết.
- **Rủi ro 2: Chi phí lưu trữ S3 phình to theo thời gian.**
- _Giảm thiểu:_ Đã áp dụng sẵn tính năng thiết lập S3 Lifecycle Policy, tự động dọn dẹp các ảnh cũ sang vùng lưu trữ sâu (Deep Archive).
- **Rủi ro 3: Throttling khi lượng ảnh đẩy lên ồ ạt.**
- _Giảm thiểu:_ Kiến trúc đã sử dụng SQS (Message Queue) đứng trước Lambda để làm bộ đệm, đảm bảo AI xử lý tuần tự, không bị rớt request.
