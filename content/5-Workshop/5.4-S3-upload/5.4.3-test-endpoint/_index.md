---
title: "Test upload"
date: 2026-07-18
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

#### 1. Call Presign API from the frontend

After signing in, select a leaf image and start upload. The frontend sends:

```json
{
  "filename": "leaf.jpg",
  "contentType": "image/jpeg"
}
```

A valid response contains:

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

#### 2. Inspect the object

```powershell
aws s3api head-object `
  --bucket $RawBucket `
  --key "<S3_KEY_FROM_RESPONSE>" `
  --region $AwsRegion `
  --query "{ContentType:ContentType,Metadata:Metadata,Encryption:ServerSideEncryption}"
```

Confirm that:

- `ContentType` is the real MIME type.
- Metadata contains `user-id` and `original-name`.
- The object uses server-side encryption.
- The key is under the authenticated user's prefix.

{{% notice warning %}}
Never capture or publish a pre-signed URL. It contains a temporary signature that can upload an object until expiration.
{{% /notice %}}

#### Deployment results

![Deployment result - frontend upload](/images/5-Workshop/5.4-S3-upload/frontend-upload.png)

![Deployment result - s3 raw object](/images/5-Workshop/5.4-S3-upload/s3-raw-object.png)
