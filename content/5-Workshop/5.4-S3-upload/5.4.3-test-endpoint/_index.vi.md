---
title: "Kiểm thử upload"
date: 2026-07-18
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### 1. Gọi Presign API từ frontend

Sau khi đăng nhập, chọn một ảnh lá và bắt đầu upload. Frontend gửi:

```json
{
  "filename": "leaf.jpg",
  "contentType": "image/jpeg"
}
```

Response hợp lệ gồm:

```json
{
  "upload_url": "<redacted>",
  "s3_key": "uploads/<cognito-sub>/<date>/<uuid>_leaf.jpg",
  "image_id": "<uuid>",
  "expiration": 900,
  "metadata": {
    "user-id": "<cognito-sub>",
    "original-name": "leaf.jpg"
  }
}
```

#### 2. Kiểm tra object

```powershell
aws s3api head-object `
  --bucket $RawBucket `
  --key "<S3_KEY_FROM_RESPONSE>" `
  --region $AwsRegion `
  --query "{ContentType:ContentType,Metadata:Metadata,Encryption:ServerSideEncryption}"
```

Xác nhận:

- `ContentType` là MIME type thật.
- Metadata có `user-id` và `original-name`.
- Object được mã hóa server-side.
- Key nằm dưới prefix của chính người dùng.

{{% notice warning %}}
Không chụp hoặc đăng pre-signed URL. URL chứa chữ ký tạm thời và có thể upload object trong thời gian còn hiệu lực.
{{% /notice %}}

#### Kết quả triển khai

![Kết quả triển khai - frontend upload](/images/5-Workshop/5.4-S3-upload/frontend-upload.png)

![Kết quả triển khai - s3 raw object](/images/5-Workshop/5.4-S3-upload/s3-raw-object.png)
