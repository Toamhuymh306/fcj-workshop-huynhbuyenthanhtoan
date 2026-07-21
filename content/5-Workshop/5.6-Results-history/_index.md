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

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![dynamodb table](/images/5-Workshop/5.6-Results-history/dynamodb-table.png)

![dynamodb user index](/images/5-Workshop/5.6-Results-history/dynamodb-user-index.png)


In DynamoDB Console:

1. Open `kts-smartagri-dev-inference-results`.
2. Choose **Indexes** → **Create index**.
3. Partition key: String `user_id`.
4. Index name: `user_id-index`.
5. Projection: **All**.
6. Wait for both the table and index to reach `ACTIVE`.

The table uses `PAY_PER_REQUEST`, so the GSI has no fixed 24/7 provisioned-capacity charge; requests and storage incur cost.

Verify:

#### 2. Create a Results Lambda execution role

The role requires:

- `dynamodb:Query` on `table/<TABLE>/index/user_id-index`.
- `dynamodb:GetItem` and `dynamodb:DeleteItem` on the table.
- `s3:DeleteObject` on raw and processed buckets.
- CloudWatch Logs write permissions.

Results Lambda does not need `PutItem`; only Inference Lambda writes results.

#### 3. Package Results Lambda

#### 4. Create Results Lambda

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![results lambda config](/images/5-Workshop/5.6-Results-history/results-lambda-config.png)


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

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![api results methods](/images/5-Workshop/5.6-Results-history/api-results-methods.png)


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

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![api stage deployment](/images/5-Workshop/5.6-Results-history/api-stage-deployment.png)


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

Complete this step in the AWS Management Console. Verify the resource name, Region, and configuration values before saving, then compare the result with the screenshots below to confirm that the resource is ready.

![ownership test](/images/5-Workshop/5.6-Results-history/ownership-test.png)


Required tests:

1. User A calls `GET /results` and sees only items with `user_id=A`.
2. User B calls `GET /results` and cannot see A's items.
3. User B deletes A's scan, receives `403`, and the item remains.
4. User A deletes A's scan, receives `200`, and the DynamoDB item, raw object, and processed object are deleted.
