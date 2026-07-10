---
title: "CORS Configuration for Frontend"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### Why is CORS Configuration necessary?

When your Web App (e.g., `http://localhost:8080`) sends a request to upload a file to S3, the browser checks if S3 allows access from that origin. Without proper CORS configuration, the browser will immediately block the request for security reasons.

#### Steps to configure CORS on Amazon S3

1. Navigate to the **Amazon S3 Console** and select the `kts-smartagri-dev-raw-images` bucket.
2. Navigate to the **Permissions** tab.
3. Scroll down to the **Cross-origin resource sharing (CORS)** section and click **Edit**.
4. Paste the following JSON into the configuration box and click **Save changes**:

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
