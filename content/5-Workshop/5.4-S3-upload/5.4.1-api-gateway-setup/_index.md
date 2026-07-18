---
title: "Create buckets, Presign Lambda, and API"
date: 2026-07-18
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

#### 1. Create three S3 buckets

```powershell
aws s3api create-bucket --bucket $RawBucket --region $AwsRegion `
  --create-bucket-configuration LocationConstraint=$AwsRegion

aws s3api create-bucket --bucket $ProcessedBucket --region $AwsRegion `
  --create-bucket-configuration LocationConstraint=$AwsRegion

aws s3api create-bucket --bucket $ArchiveBucket --region $AwsRegion `
  --create-bucket-configuration LocationConstraint=$AwsRegion
```

Enable Block Public Access for all buckets. The application does not require public buckets or objects.

#### 2. Create a Presign Lambda execution role

The role needs:

- CloudWatch Logs write permissions.
- `s3:PutObject` on `arn:aws:s3:::<RAW_BUCKET>/*`.
- A trust policy for `lambda.amazonaws.com`.

{{% notice warning %}}
Do not grant `AmazonS3FullAccess`. Presign Lambda only needs to generate upload URLs for the raw bucket.
{{% /notice %}}

#### 3. Package the source

```powershell
cd D:\kts-smart-agri\ai-service

$PresignSource = Resolve-Path "..\frontend-app\backend\lambda\presign_handler.py"
$Stage = Join-Path $env:TEMP "kts-presign-package"
$Zip = Join-Path $env:TEMP "kts-presign.zip"

New-Item -ItemType Directory -Force -Path $Stage | Out-Null
Copy-Item $PresignSource (Join-Path $Stage "lambda_function.py") -Force
Compress-Archive -Path (Join-Path $Stage "lambda_function.py") -DestinationPath $Zip -Force
tar -tf $Zip
```

The archive must contain `lambda_function.py` at its root.

#### 4. Create or update Lambda

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

For an existing function:

```powershell
aws lambda update-function-code `
  --function-name kts-smartagri-dev-presign-lambda `
  --zip-file "fileb://$Zip" `
  --region $AwsRegion
```

#### 5. Create the REST API route

In API Gateway:

1. Create REST API `kts-smartagri-api`.
2. Create resource `/presign`.
3. Create method `POST`.
4. Authorization: **Cognito User Pool Authorizer**.
5. Integration: **Lambda proxy** to Presign Lambda.
6. Add `OPTIONS` for preflight requests.
7. Deploy to stage `dev`.

#### Deployment results

![Deployment result - s3 three buckets](/images/5-Workshop/5.4-S3-upload/s3-three-buckets.png)

![Deployment result - presign lambda config](/images/5-Workshop/5.4-S3-upload/presign-lambda-config.png)

![Deployment result - api presign method](/images/5-Workshop/5.4-S3-upload/api-presign-method.png)
