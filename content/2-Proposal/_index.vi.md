---
title: "Bản đề xuất"
date: 2026-07-20
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

## KTs Smart Agriculture

### Nền tảng serverless nhận diện bệnh cây trồng trên AWS

**Mã nguồn:** [github.com/Toamhuymh306/kts-smart-agri](https://github.com/Toamhuymh306/kts-smart-agri)

## 1. Tóm tắt đề xuất

KTs Smart Agriculture là ứng dụng web hỗ trợ nhận diện bệnh cây trồng từ ảnh lá. Người dùng đăng ký và xác minh email bằng Amazon Cognito, tải ảnh trực tiếp lên Amazon S3 qua pre-signed URL, sau đó pipeline S3 → SQS → Lambda container thực hiện kiểm tra ảnh và suy luận bằng mô hình ResNet-50 đã huấn luyện trên PlantVillage. Kết quả được lưu theo từng người dùng trong Amazon DynamoDB; ảnh có chú thích được lưu trong processed bucket để hiển thị trên giao diện.

Giải pháp sử dụng kiến trúc serverless, hướng sự kiện nhằm giảm vận hành máy chủ cố định, tách luồng upload khỏi luồng AI và cho phép kiểm soát khả năng mở rộng bằng SQS/Lambda concurrency. Mục tiêu của đồ án là xây dựng một bản triển khai có thể kiểm thử và bàn giao trong 12 tuần, không tuyên bố thay thế chẩn đoán chuyên môn ngoài thực địa.

## 2. Bài toán và phạm vi

### 2.1. Bài toán

- Upload ảnh qua backend truyền thống làm tăng băng thông và có thể chạm giới hạn payload của API.
- PyTorch và model weights vượt giới hạn phù hợp của Lambda ZIP package.
- AI inference có thời gian xử lý không ổn định; xử lý đồng bộ dễ gây timeout cho frontend.
- Kết quả phải được cô lập theo người dùng và không được phép đọc/xóa chéo dữ liệu.

### 2.2. Giải pháp đề xuất

- API Gateway và Presign Lambda cấp pre-signed URL ngắn hạn; trình duyệt PUT ảnh trực tiếp lên raw S3 bucket.
- S3 Event Notification gửi message vào SQS để tách upload khỏi inference và hỗ trợ retry.
- Inference Lambda sử dụng container image trên ECR, chạy Rekognition validation gate trước khi suy luận ResNet-50.
- DynamoDB lưu trạng thái, dự đoán và ownership; Results Lambda cung cấp API xem, liệt kê và xóa lịch sử.
- CloudWatch tập trung log/metric. CloudFront, WAF, CloudTrail và SNS alarm là lớp production hardening tùy chọn, chỉ ghi là “đã triển khai” khi có minh chứng cấu hình.

### 2.3. Phạm vi mô hình AI

- Dataset: PlantVillage, 14 loại cây và 38 lớp khỏe/bệnh.
- Model production: ResNet-50; LeNet được giữ làm mô hình benchmark.
- Input: ảnh được resize về `224 × 224` và chuẩn hóa theo ImageNet.
- Validation gate: chỉ chạy classifier khi Amazon Rekognition phát hiện cả `Leaf` và `Plant` đạt ngưỡng cấu hình.
- Output: cây trồng, bệnh, confidence, top-k, thời gian xử lý và ảnh đã chú thích.

{{% notice warning %}}
PlantVillage chủ yếu gồm ảnh lá trong điều kiện tương đối sạch. Độ chính xác trên tập test không bảo đảm hiệu quả tương đương với ảnh ngoài đồng ruộng có nền phức tạp, thiếu sáng hoặc nhiều lá chồng lấp.
{{% /notice %}}

## 3. Kiến trúc giải pháp

![Kiến trúc KTs Smart Agriculture](/images/2-Proposal/diagram1.png)

### 3.1. Luồng xử lý chính

1. Người dùng đăng ký, xác minh email và nhận JWT từ Amazon Cognito.
2. Frontend gọi `POST /presign` qua API Gateway với JWT.
3. Presign Lambda lấy `sub` từ Cognito claims, tạo `image_id`, S3 key và pre-signed URL có metadata `user-id` đã ký.
4. Trình duyệt PUT ảnh trực tiếp vào raw S3 bucket; ảnh không đi qua API Gateway/Lambda payload.
5. S3 gửi `s3:ObjectCreated:*` vào inference SQS queue.
6. SQS event source mapping gọi Inference Lambda với batch size `1`.
7. Lambda gọi Rekognition `DetectLabels`. Ảnh không hợp lệ được ghi `REJECTED`; ảnh hợp lệ được suy luận bằng ResNet-50.
8. Lambda ghi kết quả vào DynamoDB và lưu ảnh chú thích vào processed S3 bucket.
9. Frontend gọi Results Lambda qua các API Cognito-protected để xem, liệt kê hoặc xóa dữ liệu đúng chủ sở hữu.
10. CloudWatch thu thập log/metric; alarm có thể gửi SNS notification trong cấu hình production.

### 3.2. Thành phần AWS

| Thành phần | Vai trò | Trạng thái trong phạm vi đồ án |
|---|---|---|
| Amazon Cognito | Đăng ký, xác minh email, đăng nhập và phát hành JWT | Lõi |
| Amazon API Gateway | REST API `/presign`, `/results`, `/results/{scanId}` | Lõi |
| Presign Lambda | Tạo pre-signed URL và gắn ownership metadata | Lõi |
| Inference Lambda | Rekognition gate, ResNet-50 inference, ghi kết quả | Lõi |
| Results Lambda | Liệt kê, đọc và xóa lịch sử đúng chủ sở hữu | Lõi |
| Amazon S3 | Raw images và processed images; frontend bucket nếu host tĩnh | Lõi/tùy cách deploy |
| Amazon SQS | Buffer S3 events, retry và kiểm soát concurrency | Lõi |
| Amazon ECR | Lưu Lambda container image; không nằm trong data path runtime | Lõi |
| Amazon DynamoDB | `image_id` partition key, `user_id-index` GSI và TTL | Lõi |
| Amazon Rekognition | Loại ảnh không phải lá trước khi chạy classifier | Lõi |
| Amazon CloudWatch | Logs, metrics, alarms và troubleshooting | Lõi |
| CloudFront, WAF, CloudTrail, SNS | CDN, bảo vệ web/API, audit và thông báo alarm | Production hardening tùy chọn |

## 4. Yêu cầu bảo mật và độ tin cậy

- Không public raw/processed buckets; chỉ dùng pre-signed URL có thời hạn.
- API Gateway dùng Cognito User Pools Authorizer; backend lấy owner từ JWT `sub`, không nhận `user_id` tùy ý từ request.
- Results Lambda kiểm tra ownership trước khi đọc/xóa DynamoDB record hoặc S3 object.
- IAM áp dụng least privilege theo từng Lambda; không dùng `AdministratorAccess` hoặc `s3:*`.
- CORS production chỉ cho phép frontend origin thực tế.
- SQS queue policy giới hạn `aws:SourceArn` và `aws:SourceAccount`.
- Visibility timeout tối thiểu sáu lần Lambda timeout; cấu hình hiện tại là `720` giây cho Lambda timeout `120` giây.
- Dùng `image_id` để xử lý idempotent; bổ sung DLQ và alarm cho message xử lý thất bại nhiều lần.
- Đặt maximum concurrency phù hợp để bảo vệ account quota và tránh làm Lambda inference sử dụng quá nhiều tài nguyên.

## 5. Kế hoạch chung của nhóm trong 12 tuần

Các mốc dưới đây là tiến độ tổng thể của đồ án. Mỗi thành viên căn cứ vào vai trò và phần việc thực tế để lập Worklog riêng, nhưng vẫn bám theo các mốc chung này.

| Tuần | Thời gian | Hạng mục | Kết quả chính |
|---:|---|---|---|
| 1 | 17/04–23/04 | Khởi động, IAM, AWS CLI, Budget | Môi trường và backlog |
| 2 | 24/04–30/04 | Chuẩn bị PlantVillage | Dataset split và preprocessing pipeline |
| 3 | 01/05–07/05 | Huấn luyện LeNet/ResNet | Checkpoint và báo cáo đánh giá |
| 4 | 08/05–14/05 | Docker và ECR | Container image, local smoke test |
| 5 | 15/05–21/05 | S3, DynamoDB, Lambda backend | Presign/results handlers và storage |
| 6 | 22/05–28/05 | API Gateway và Cognito | JWT-protected API và CORS |
| 7 | 29/05–04/06 | Frontend | Auth, upload và history UI |
| 8 | 05/06–11/06 | MVP end-to-end | Luồng cơ bản và defect backlog |
| 9 | 12/06–18/06 | S3 → SQS → Lambda | Hai nhánh `COMPLETED`/`REJECTED` |
| 10 | 19/06–25/06 | Security/integration testing | Kiểm thử ownership và delete |
| 11 | 26/06–02/07 | CloudWatch và tối ưu | Metrics, runbook và cost review |
| 12 | 03/07–10/07 | Nghiệm thu và bàn giao | Release, workshop, demo và báo cáo |

## 6. Tiêu chí nghiệm thu

- Đăng ký, xác minh email, đăng nhập và đăng xuất hoạt động.
- API từ chối request thiếu/sai JWT; User A không xem hoặc xóa dữ liệu User B.
- JPEG, PNG và WebP hợp lệ upload trực tiếp lên S3 qua pre-signed URL.
- Ảnh lá hợp lệ tạo record `COMPLETED` và processed image.
- Ảnh không phải lá tạo record `REJECTED` và không tạo processed image.
- SQS queue trở về trạng thái không tồn đọng sau khi xử lý ổn định; lỗi lặp lại được chuyển sang DLQ.
- CloudWatch không còn lỗi checkpoint, metadata, environment variable hoặc timeout trong test cuối.
- Source code, hướng dẫn triển khai, workshop song ngữ, test report và demo được bàn giao.

## 7. Ước tính chi phí

Không sử dụng con số “dưới 1 USD/tháng” như một cam kết. Chi phí phải được tính theo Region `ap-southeast-1`, trạng thái Free Tier của tài khoản và số liệu đo thực tế.

**Giả định để lập estimate:** 1.000 ảnh/tháng, kích thước ảnh trung bình được ghi rõ, Inference Lambda `2048 MB`, timeout `120` giây, batch size `1`; thời gian chạy trung bình/p95 lấy từ CloudWatch sau load test.

| Dịch vụ | Cost driver cần đưa vào AWS Pricing Calculator |
|---|---|
| Lambda | Số request, GB-second, architecture, average/p95 duration, ephemeral storage nếu tăng |
| Rekognition | Số ảnh gọi `DetectLabels` |
| S3 | Dung lượng raw/processed, PUT/GET, lifecycle transition, retrieval và data transfer |
| SQS | Send/Receive/Delete requests và retry |
| DynamoDB | Read/write request units hoặc on-demand requests, storage và backup |
| API Gateway | Số API calls và data transfer |
| ECR | Dung lượng container image và data transfer liên Region nếu có |
| CloudWatch | Log ingestion, retention, metrics và alarms |
| CloudFront/WAF/SNS | Chỉ cộng khi các lớp production tùy chọn được bật |

S3 Glacier Flexible Retrieval và Deep Archive chỉ dùng cho dữ liệu không cần truy cập tức thời; cần tính minimum storage duration, transition/retrieval request và early-deletion charge. Với ảnh nhỏ hoặc cần hiển thị lịch sử, expiration hoặc S3 Intelligent-Tiering có thể phù hợp hơn archive cố định sau 30 ngày.

## 8. Rủi ro và giảm thiểu

| Rủi ro | Ảnh hưởng | Giảm thiểu |
|---|---|---|
| Cold start/model load chậm | Tăng latency, timeout | Giảm image/model size, đo p95, điều chỉnh memory/timeout; chỉ dùng Provisioned Concurrency khi chi phí cho phép |
| Ảnh ngoài miền PlantVillage | Dự đoán sai | Rekognition gate, hướng dẫn chụp ảnh, confidence threshold, bổ sung dữ liệu thực địa |
| SQS gửi trùng/at-least-once | Ghi trùng kết quả | Idempotency theo `image_id`, conditional write và DLQ |
| Traffic tăng đột biến | Throttling hoặc tăng chi phí | Maximum/reserved concurrency, Budget alarm và rate limiting/WAF khi production |
| Truy cập chéo người dùng | Rò rỉ hoặc xóa dữ liệu | Cognito authorizer, owner từ JWT, GSI theo `user_id`, authorization test |
| Archive không phù hợp | Không xem được ảnh tức thời, phí retrieval | Phân loại retention theo use case; kiểm tra minimum duration trước khi bật lifecycle |

## 9. Tài liệu tham khảo

- [AWS Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html)
- [API Gateway quotas](https://docs.aws.amazon.com/apigateway/latest/developerguide/http-api-quotas.html)
- [Using AWS Lambda with Amazon SQS](https://docs.aws.amazon.com/lambda/latest/dg/with-sqs.html)
- [Amazon S3 Lifecycle transition considerations](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-transition-general-considerations.html)
- [AWS Pricing Calculator](https://calculator.aws/)
