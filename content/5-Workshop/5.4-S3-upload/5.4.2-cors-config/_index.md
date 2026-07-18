---
title: "Configure CORS"
date: 2026-07-18
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### S3 CORS

Create the configuration file:

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

Replace localhost with the production CloudFront/frontend origin. Avoid `*` when it is unnecessary.

#### API Gateway CORS

`OPTIONS /presign` must return:

```text
Access-Control-Allow-Origin: <frontend-origin>
Access-Control-Allow-Headers: Authorization,Content-Type
Access-Control-Allow-Methods: POST,OPTIONS
```

Presign Lambda must also include matching CORS headers in actual responses.

#### Test preflight

```powershell
curl.exe -i -X OPTIONS "$ApiBaseUrl/presign" `
  -H "Origin: http://127.0.0.1:8000" `
  -H "Access-Control-Request-Method: POST" `
  -H "Access-Control-Request-Headers: Authorization,Content-Type"
```

Expect `HTTP/1.1 200 OK` and all three CORS headers.

#### Deployment results

![Deployment result - s3 cors](/images/5-Workshop/5.4-S3-upload/s3-cors.png)

![Deployment result - api presign options](/images/5-Workshop/5.4-S3-upload/api-presign-options.png)

![Deployment result - presign preflight](/images/5-Workshop/5.4-S3-upload/presign-preflight.png)
