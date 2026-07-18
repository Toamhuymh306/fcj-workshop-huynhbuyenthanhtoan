---
title: "Cấu hình CORS"
date: 2026-07-18
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### S3 CORS

Tạo file cấu hình:

```powershell
$CorsFile = Join-Path $env:TEMP "kts-s3-cors.json"

@'
{
  "CORSRules": [
    {
      "AllowedOrigins": ["http://127.0.0.1:8000"],
      "AllowedMethods": ["PUT", "POST", "GET", "HEAD"],
      "AllowedHeaders": ["*"],
      "ExposeHeaders": ["ETag"]
    }
  ]
}
'@ | Set-Content -Encoding utf8 $CorsFile

aws s3api put-bucket-cors `
  --bucket $RawBucket `
  --cors-configuration "file://$CorsFile" `
  --region $AwsRegion
```

Trong production, thay localhost bằng domain CloudFront/frontend thật. Không dùng `*` khi không cần thiết.

#### API Gateway CORS

`OPTIONS /presign` phải trả về:

```text
Access-Control-Allow-Origin: <frontend-origin>
Access-Control-Allow-Headers: Authorization,Content-Type
Access-Control-Allow-Methods: POST,OPTIONS
```

Presign Lambda cũng phải trả các header CORS tương ứng trong response thật.

#### Kiểm tra preflight

```powershell
curl.exe -i -X OPTIONS "$ApiBaseUrl/presign" `
  -H "Origin: http://127.0.0.1:8000" `
  -H "Access-Control-Request-Method: POST" `
  -H "Access-Control-Request-Headers: Authorization,Content-Type"
```

Kỳ vọng `HTTP/1.1 200 OK` và đủ ba header CORS.

#### Kết quả triển khai

![Kết quả triển khai - s3 cors](/images/5-Workshop/5.4-S3-upload/s3-cors.png)

![Kết quả triển khai - api presign options](/images/5-Workshop/5.4-S3-upload/api-presign-options.png)

![Kết quả triển khai - presign preflight](/images/5-Workshop/5.4-S3-upload/presign-preflight.png)
