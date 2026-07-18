---
title: "Tạo bucket, Presign Lambda và API"
date: 2026-07-18
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### 1. Tạo ba S3 bucket

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![s3 three buckets](/images/5-Workshop/5.4-S3-upload/s3-three-buckets.png)


Bật Block Public Access cho cả ba bucket. Ứng dụng không cần public bucket hoặc public object.

#### 2. Tạo execution role cho Presign Lambda

Role cần:

- Quyền ghi CloudWatch Logs.
- `s3:PutObject` trên `arn:aws:s3:::<RAW_BUCKET>/*`.
- Trust policy cho service principal `lambda.amazonaws.com`.

{{% notice warning %}}
Không cấp `AmazonS3FullAccess`. Presign Lambda chỉ cần tạo URL cho object trong raw bucket.
{{% /notice %}}

#### 3. Đóng gói source code

Zip phải chứa `lambda_function.py` ở thư mục gốc.

#### 4. Tạo hoặc cập nhật Lambda

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![presign lambda config](/images/5-Workshop/5.4-S3-upload/presign-lambda-config.png)


Thiết lập:

| Thuộc tính | Giá trị |
|---|---|
| Function name | `kts-smartagri-dev-presign-lambda` |
| Runtime | Python 3.12 |
| Handler | `lambda_function.lambda_handler` |
| Timeout | 10 giây |
| Memory | 128 MB |
| `S3_BUCKET` | raw bucket |
| `CORS_ORIGIN` | domain frontend hoặc `*` cho lab |


#### 5. Tạo REST API route

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![api presign method](/images/5-Workshop/5.4-S3-upload/api-presign-method.png)


Trong API Gateway:

1. Tạo REST API `kts-smartagri-api`.
2. Tạo resource `/presign`.
3. Tạo method `POST`.
4. Authorization: **Cognito User Pool Authorizer**.
5. Integration: **Lambda proxy** đến Presign Lambda.
6. Tạo method `OPTIONS` để phục vụ preflight.
7. Deploy vào stage `dev`.
