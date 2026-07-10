---
title: "AI Inference"
date: 2026-07-10
weight: 5
chapter: false
pre: " <b> 5.5 </b> "
---

Trong phần này, chúng ta sẽ cấu hình **AI Inference** để phân tích hình ảnh cây trồng sau khi người dùng tải ảnh thành công lên Amazon S3. Hệ thống sử dụng **AWS Lambda (Container Image)** để chạy mô hình học sâu (Deep Learning), dự đoán bệnh trên lá cây và lưu kết quả phân tích vào Amazon DynamoDB.

Luồng xử lý này hoàn toàn **Serverless**, giúp hệ thống tự động mở rộng theo số lượng yêu cầu mà không cần quản lý máy chủ.

![AI Inference Architecture](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.5-AI-inference/ai-inference-flow.png)

#### Luồng xử lý AI

1. Sau khi người dùng tải ảnh lên **Amazon S3 (Raw Images Bucket)**, Frontend gửi yêu cầu phân tích đến **Amazon API Gateway**.

2. API Gateway xác thực JWT Token từ **Amazon Cognito** nhằm đảm bảo chỉ người dùng đã đăng nhập mới được phép sử dụng dịch vụ AI.

![API Gateway](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.5-AI-inference/api-gateway.png)

3. API Gateway kích hoạt **Inference Lambda** được triển khai dưới dạng **Container Image** lưu trữ trên **Amazon ECR**.

![Lambda Container](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.5-AI-inference/lambda-container.png)

4. Lambda tải ảnh từ **Amazon S3**, thực hiện các bước tiền xử lý gồm:

- Resize ảnh về kích thước của mô hình.
- Chuẩn hóa dữ liệu đầu vào.
- Chuyển đổi Tensor.
- Nạp mô hình AI.

![Image Preprocessing](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.5-AI-inference/preprocessing.png)

5. Mô hình AI tiến hành suy luận (Inference) để dự đoán loại bệnh của cây trồng và tính toán độ tin cậy (Confidence Score).

Ví dụ kết quả:

```json
{
  "class": "Tomato___Early_blight",
  "confidence": 0.9876
}
```

![Prediction Result](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.5-AI-inference/prediction.png)

6. Sau khi suy luận thành công, Lambda lưu toàn bộ kết quả vào **Amazon DynamoDB**, bao gồm:

- User ID
- Tên ảnh
- Loại bệnh
- Confidence
- Thời gian phân tích

![DynamoDB](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.5-AI-inference/dynamodb.png)

7. Nếu Confidence thấp hơn ngưỡng cho phép hoặc phát hiện bệnh nghiêm trọng, Lambda sẽ gửi thông báo thông qua **Amazon SNS** để cảnh báo người dùng.

![SNS](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.5-AI-inference/sns.png)

8. Frontend nhận phản hồi từ API Gateway và hiển thị kết quả phân tích cho người dùng.

Ví dụ:

```
Plant : Tomato

Disease : Early Blight

Confidence : 98.76%

Status : Success
```

![Frontend Result](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.5-AI-inference/frontend-result.png)

---

#### Tổng kết

Trong phần này chúng ta đã triển khai thành công dịch vụ **AI Inference** trên nền tảng AWS Serverless.

Toàn bộ quy trình bao gồm:

- API Gateway tiếp nhận yêu cầu.
- Xác thực người dùng bằng Amazon Cognito.
- AWS Lambda (Container Image) thực hiện suy luận AI.
- Amazon S3 lưu trữ ảnh đầu vào.
- Amazon DynamoDB lưu lịch sử phân tích.
- Amazon SNS gửi cảnh báo khi cần thiết.

Kiến trúc này giúp hệ thống mở rộng linh hoạt, giảm chi phí vận hành và đáp ứng tốt các yêu cầu phân tích hình ảnh cây trồng theo thời gian thực.
