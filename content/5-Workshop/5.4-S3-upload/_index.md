---
title: "Secure upload and event pipeline"
date: 2026-07-18
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

#### Objective

The frontend does not send image files through Lambda or API Gateway. Presign Lambda returns a time-limited URL so the browser uploads directly to the raw S3 bucket. Signed `user-id` metadata becomes the ownership source throughout the pipeline.

![Architecture Flow](/images/5-Workshop/5.4-S3-upload/diagram3.png)

After upload, S3 sends an event to SQS. The queue absorbs bursts, retains messages during Lambda failures, and decouples upload latency from inference latency.

#### Storage layers

| Bucket | Purpose |
|---|---|
| `kts-smartagri-dev-raw-images` | Original user uploads |
| `kts-smartagri-dev-processed-images` | Labeled JPEG output images |
| `kts-smartagri-dev-archive-images` | Archive and administrative test data |

#### Contents

1. [Create buckets, Presign Lambda, and API](5.4.1-api-gateway-setup/)
2. [Configure CORS](5.4.2-cors-config/)
3. [Test upload](5.4.3-test-endpoint/)
4. [Connect S3 to SQS](5.4.4-event-pipeline/)
