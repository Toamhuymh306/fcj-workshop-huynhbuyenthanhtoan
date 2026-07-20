---
title: "Kiểm thử end-to-end và giám sát"
date: 2026-07-18
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

#### Chuẩn bị

1. Chạy frontend tại `http://127.0.0.1:8000`.
2. Đăng nhập bằng tài khoản Cognito đã xác minh email.
3. Mở DevTools Network để quan sát `/presign`, upload S3 và `/results`.
4. Không hiển thị JWT hoặc pre-signed URL khi chụp màn hình.

#### Video demo hệ thống

Video dưới đây minh họa luồng sử dụng website KTs Smart Agriculture từ đăng nhập, tải ảnh lá cây, nhận kết quả dự đoán đến xem lịch sử quét.

{{< youtube PSYT_v13Lr4 >}}

#### Test 1: Ảnh lá hợp lệ

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![valid leaf result](/images/5-Workshop/5.7-End-to-end/valid-leaf-result.png)

![processed object](/images/5-Workshop/5.7-End-to-end/processed-object.png)


Tải một ảnh từ PlantVillage validation set.

Kỳ vọng:

- Presign API trả `200`.
- S3 raw object có metadata `user-id`.
- SQS message được xử lý và queue trở về trạng thái không backlog.
- Rekognition trả `Leaf` và `Plant` từ 90% trở lên.
- DynamoDB item có `status=COMPLETED`, prediction, confidence và `modelName=resnet`.
- Processed bucket có JPEG với `Content-Type=image/jpeg`.
- Frontend hiển thị cây, bệnh và confidence.

#### Test 2: Ảnh không hợp lệ

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![invalid image result](/images/5-Workshop/5.7-End-to-end/invalid-image-result.png)


Tải một sơ đồ, ảnh người hoặc ảnh động vật.

Kỳ vọng:

- Rekognition không trả đồng thời `Leaf` và `Plant`.
- DynamoDB item có `status=REJECTED` và `rejectionReason=NOT_A_LEAF_IMAGE`.
- Không có processed image.
- Frontend hiển thị **Ảnh không hợp lệ, vui lòng chọn ảnh lá cây rõ nét.**
- ResNet không chạy cho ảnh này.

#### Test 3: Results API và CORS

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![scan history](/images/5-Workshop/5.7-End-to-end/scan-history.png)


Không có token, `GET /results` phải trả `401` kèm CORS headers. Có ID token hợp lệ, route trả `200` và chỉ trả lịch sử của user đó.

#### CloudWatch Logs

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![cloudwatch inference log](/images/5-Workshop/5.7-End-to-end/cloudwatch-inference-log.png)


Kiểm tra ba log group:

```text
/aws/lambda/kts-smartagri-dev-presign-lambda
/aws/lambda/kts-smartagri-dev-inference-lambda
/aws/lambda/kts-smartagri-dev-results-lambda
```

Inference log cần có:

- Model load duration và checkpoint path.
- Rekognition validation result.
- Inference duration cho ảnh hợp lệ.
- Không log JWT, pre-signed URL hoặc image bytes.

#### Metric cần theo dõi

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![cloudwatch metrics](/images/5-Workshop/5.7-End-to-end/cloudwatch-metrics.png)


- Lambda `Errors`, `Duration`, `Throttles` và `ConcurrentExecutions`.
- SQS `ApproximateNumberOfMessagesVisible` và age of oldest message.
- API Gateway `4XXError`, `5XXError`, `Latency`.
- DynamoDB throttled requests.

#### Tiêu chí hoàn thành

Workshop hoàn thành khi cả ba test đạt yêu cầu, không có message tồn đọng ngoài dự kiến và không có dữ liệu user A xuất hiện trong session user B.
