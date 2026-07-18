---
title: "Tạo bucket, Presign Lambda và API"
date: 2026-07-18
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### 1. Tạo ba S3 bucket

```powershell
aws s3api create-bucket --bucket $RawBucket --region $AwsRegion `
  --create-bucket-configuration LocationConstraint=$AwsRegion

aws s3api create-bucket --bucket $ProcessedBucket --region $AwsRegion `
  --create-bucket-configuration LocationConstraint=$AwsRegion

aws s3api create-bucket --bucket $ArchiveBucket --region $AwsRegion `
  --create-bucket-configuration LocationConstraint=$AwsRegion
```

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

```powershell
cd D:\kts-smart-agri\ai-service

$PresignSource = Resolve-Path "..\frontend-app\backend\lambda\presign_handler.py"
$Stage = Join-Path $env:TEMP "kts-presign-package"
$Zip = Join-Path $env:TEMP "kts-presign.zip"

New-Item -ItemType Directory -Force -Path $Stage | Out-Null
Copy-Item $PresignSource (Join-Path $Stage "lambda_function.py") -Force
Compress-Archive -Path (Join-Path $Stage "lambda_function.py") -DestinationPath $Zip -Force
tar -tf $Zip
```

Zip phải chứa `lambda_function.py` ở thư mục gốc.

#### 4. Tạo hoặc cập nhật Lambda

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

Với function đã tồn tại:

```powershell
aws lambda update-function-code `
  --function-name kts-smartagri-dev-presign-lambda `
  --zip-file "fileb://$Zip" `
  --region $AwsRegion
```

#### 5. Tạo REST API route

Trong API Gateway:

1. Tạo REST API `kts-smartagri-api`.
2. Tạo resource `/presign`.
3. Tạo method `POST`.
4. Authorization: **Cognito User Pool Authorizer**.
5. Integration: **Lambda proxy** đến Presign Lambda.
6. Tạo method `OPTIONS` để phục vụ preflight.
7. Deploy vào stage `dev`.

#### Kết quả triển khai

![Kết quả triển khai - s3 three buckets](/images/5-Workshop/5.4-S3-upload/s3-three-buckets.png)

![Kết quả triển khai - presign lambda config](/images/5-Workshop/5.4-S3-upload/presign-lambda-config.png)

![Kết quả triển khai - api presign method](/images/5-Workshop/5.4-S3-upload/api-presign-method.png)
