---
title: "Configure CORS"
date: 2026-07-18
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### S3 CORS

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![s3 cors](/images/5-Workshop/5.4-S3-upload/s3-cors.png)


Create the configuration file:

Replace localhost with the production CloudFront/frontend origin. Avoid `*` when it is unnecessary.

#### API Gateway CORS

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![api presign options](/images/5-Workshop/5.4-S3-upload/api-presign-options.png)


`OPTIONS /presign` must return:

```text
Access-Control-Allow-Origin: <frontend-origin>
Access-Control-Allow-Headers: Authorization,Content-Type
Access-Control-Allow-Methods: POST,OPTIONS
```

Presign Lambda must also include matching CORS headers in actual responses.

#### Test preflight

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![presign preflight](/images/5-Workshop/5.4-S3-upload/presign-preflight.png)


Expect `HTTP/1.1 200 OK` and all three CORS headers.
