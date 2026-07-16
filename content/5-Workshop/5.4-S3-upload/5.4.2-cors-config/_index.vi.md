---
title : "Cấu hình CORS cho Frontend"
date : 2026-07-10 
weight : 2
chapter : false
pre : " <b> 5.4.2. </b> "
---

#### Tại sao cần cấu hình CORS?

Khi Web App của bạn (ví dụ: `http://localhost:8080`) gửi yêu cầu tải file lên S3, trình duyệt sẽ kiểm tra xem S3 có cho phép trang web đó truy cập hay không. Nếu không có cấu hình CORS, trình duyệt sẽ chặn yêu cầu này ngay lập tức vì lý do bảo mật.

#### Các bước cấu hình trên Amazon S3

1. Truy cập vào **Amazon S3 Console** và chọn bucket `kts-smartagri-dev-raw-images`.
2. Chuyển sang tab **Permissions** (Quyền).
3. Cuộn xuống phần **Cross-origin resource sharing (CORS)** và nhấp vào **Edit**.
4. Dán đoạn JSON dưới đây vào khung cấu hình và nhấn **Save changes**:

```json
[
    {
        "AllowedHeaders": ["*"],
        "AllowedMethods": ["PUT", "POST", "GET"],
        "AllowedOrigins": ["*"],
        "ExposeHeaders": ["ETag"],
        "MaxAgeSeconds": 3000
    }
]
```