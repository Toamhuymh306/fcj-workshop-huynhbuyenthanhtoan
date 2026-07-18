---
title: "Kiểm thử upload"
date: 2026-07-18
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### 1. Gọi Presign API từ frontend

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![frontend upload](/images/5-Workshop/5.4-S3-upload/frontend-upload.png)


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

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![s3 raw object](/images/5-Workshop/5.4-S3-upload/s3-raw-object.png)


Xác nhận:

- `ContentType` là MIME type thật.
- Metadata có `user-id` và `original-name`.
- Object được mã hóa server-side.
- Key nằm dưới prefix của chính người dùng.

{{% notice warning %}}
Không chụp hoặc đăng pre-signed URL. URL chứa chữ ký tạm thời và có thể upload object trong thời gian còn hiệu lực.
{{% /notice %}}
