---
title: "Results history and data authorization"
date: 2026-07-18
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

#### Objective

Inference Lambda writes every scan to DynamoDB with partition key `image_id` and owner `user_id`. Results Lambda exposes three Cognito-protected operations:

```text
GET    /results
GET    /results/{scanId}
DELETE /results/{scanId}
```

Every operation obtains the user from the `sub` claim. The client cannot submit a trusted `user_id` to control authorization.

#### 1. Create `user_id-index`

In DynamoDB Console:

1. Open `kts-smartagri-dev-inference-results`.
2. Choose **Indexes** → **Create index**.
3. Partition key: String `user_id`.
4. Index name: `user_id-index`.
5. Projection: **All**.
6. Wait for both the table and index to reach `ACTIVE`.

The table uses `PAY_PER_REQUEST`, so the GSI has no fixed 24/7 provisioned-capacity charge; requests and storage incur cost.

Verify:

```powershell
aws dynamodb describe-table `
  --table-name $ResultTable `
  --region $AwsRegion `
  --query "Table.{TableStatus:TableStatus,BillingMode:BillingModeSummary.BillingMode,Index:GlobalSecondaryIndexes[?IndexName=='user_id-index']|[0].{Status:IndexStatus,Key:KeySchema,Projection:Projection}}"
```

#### 2. Create a Results Lambda execution role

The role requires:

- `dynamodb:Query` on `table/<TABLE>/index/user_id-index`.
- `dynamodb:GetItem` and `dynamodb:DeleteItem` on the table.
- `s3:DeleteObject` on raw and processed buckets.
- CloudWatch Logs write permissions.

Results Lambda does not need `PutItem`; only Inference Lambda writes results.

#### 3. Package Results Lambda

```powershell
$ResultsSource = Resolve-Path "D:\kts-smart-agri\frontend-app\backend\lambda\results_handler.py"
$Stage = Join-Path $env:TEMP "kts-results-package"
$Zip = Join-Path $env:TEMP "kts-results.zip"

New-Item -ItemType Directory -Force -Path $Stage | Out-Null
Copy-Item $ResultsSource (Join-Path $Stage "lambda_function.py") -Force
Compress-Archive -Path (Join-Path $Stage "lambda_function.py") -DestinationPath $Zip -Force
tar -tf $Zip
```

#### 4. Create Results Lambda

| Property | Value |
|---|---|
| Function name | `kts-smartagri-dev-results-lambda` |
| Runtime | Python 3.12 |
| Handler | `lambda_function.lambda_handler` |
| Memory | 256 MB |
| Timeout | 15 seconds |
| `RESULTS_TABLE` | result table |
| `USER_INDEX` | `user_id-index` |
| `RAW_IMAGES_BUCKET` | raw bucket |
| `RESULTS_BUCKET` | processed bucket |
| `CORS_ORIGIN` | frontend origin |

#### 5. Create API Gateway routes

Use the Cognito Authorizer for every real method:

| Resource | Method | Authorization | Integration |
|---|---|---|---|
| `/results` | GET | Cognito User Pools | Results Lambda proxy |
| `/results` | OPTIONS | NONE | MOCK |
| `/results/{scanId}` | GET | Cognito User Pools | Results Lambda proxy |
| `/results/{scanId}` | DELETE | Cognito User Pools | Results Lambda proxy |
| `/results/{scanId}` | OPTIONS | NONE | MOCK |

Allow API Gateway to invoke Lambda with a source ARN restricted to this REST API and `/results*` path.

#### 6. Configure CORS and deploy

`OPTIONS /results`:

```text
Access-Control-Allow-Methods: GET,OPTIONS
```

`OPTIONS /results/{scanId}`:

```text
Access-Control-Allow-Methods: GET,DELETE,OPTIONS
```

Both allow `Authorization,Content-Type`. Add CORS headers to `DEFAULT_4XX` and `DEFAULT_5XX` Gateway Responses so browsers can read 401/500 errors.

After any API change, create a deployment and point stage `dev` to the new deployment.

#### 7. Test ownership

Required tests:

1. User A calls `GET /results` and sees only items with `user_id=A`.
2. User B calls `GET /results` and cannot see A's items.
3. User B deletes A's scan, receives `403`, and the item remains.
4. User A deletes A's scan, receives `200`, and the DynamoDB item, raw object, and processed object are deleted.

{{% notice warning %}}
Do not use DynamoDB `Scan` and filter in application code. Querying `user_id-index` reduces cost and prevents accidental cross-user reads.
{{% /notice %}}

#### Deployment results

![Deployment result - dynamodb table](/images/5-Workshop/5.6-Results-history/dynamodb-table.png)

![Deployment result - dynamodb user index](/images/5-Workshop/5.6-Results-history/dynamodb-user-index.png)

![Deployment result - results lambda config](/images/5-Workshop/5.6-Results-history/results-lambda-config.png)

![Deployment result - api results methods](/images/5-Workshop/5.6-Results-history/api-results-methods.png)

![Deployment result - api stage deployment](/images/5-Workshop/5.6-Results-history/api-stage-deployment.png)

![Deployment result - ownership test](/images/5-Workshop/5.6-Results-history/ownership-test.png)
