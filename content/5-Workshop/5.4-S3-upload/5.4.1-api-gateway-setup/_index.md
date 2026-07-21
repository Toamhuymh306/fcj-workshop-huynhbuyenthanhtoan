---
title: "Create buckets, Presign Lambda, and API"
date: 2026-07-18
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### 1. Create three S3 buckets

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![s3 three buckets](/images/5-Workshop/5.4-S3-upload/s3-three-buckets.png)


Enable Block Public Access for all buckets. The application does not require public buckets or objects.

#### 2. Create a Presign Lambda execution role

The role needs:

- CloudWatch Logs write permissions.
- `s3:PutObject` on `arn:aws:s3:::<RAW_BUCKET>/*`.
- A trust policy for `lambda.amazonaws.com`.

#### 3. Package the source

The archive must contain `lambda_function.py` at its root.

#### 4. Create or update Lambda

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![presign lambda config](/images/5-Workshop/5.4-S3-upload/presign-lambda-config.png)


Use these settings:

| Property | Value |
|---|---|
| Function name | `kts-smartagri-dev-presign-lambda` |
| Runtime | Python 3.12 |
| Handler | `lambda_function.lambda_handler` |
| Timeout | 10 seconds |
| Memory | 128 MB |
| `S3_BUCKET` | raw bucket |
| `CORS_ORIGIN` | frontend origin or `*` for the lab |


#### 5. Create the REST API route

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![api presign method](/images/5-Workshop/5.4-S3-upload/api-presign-method.png)


In API Gateway:

1. Create REST API `kts-smartagri-api`.
2. Create resource `/presign`.
3. Create method `POST`.
4. Authorization: **Cognito User Pool Authorizer**.
5. Integration: **Lambda proxy** to Presign Lambda.
6. Add `OPTIONS` for preflight requests.
7. Deploy to stage `dev`.
