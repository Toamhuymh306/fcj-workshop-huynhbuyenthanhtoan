---
title: "Lịch sử kết quả và phân quyền dữ liệu"
date: 2026-07-18
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

#### Mục tiêu

Inference Lambda ghi mỗi lần quét vào DynamoDB với partition key `image_id` và owner `user_id`. Results Lambda cung cấp ba thao tác được Cognito bảo vệ:

```text
GET    /results
GET    /results/{scanId}
DELETE /results/{scanId}
```

Mọi thao tác đều lấy người dùng từ claim `sub`; client không được tự gửi `user_id` để quyết định quyền truy cập.

#### 1. Tạo GSI `user_id-index`

Trong DynamoDB Console:

1. Mở table `kts-smartagri-dev-inference-results`.
2. Chọn **Indexes** → **Create index**.
3. Partition key: `user_id` kiểu String.
4. Index name: `user_id-index`.
5. Projection: **All**.
6. Chờ table và index cùng chuyển sang `ACTIVE`.

Table dùng `PAY_PER_REQUEST`, vì vậy GSI không có chi phí chạy 24/24 cố định; chi phí phát sinh từ request và storage.

Kiểm tra:

```powershell
aws dynamodb describe-table `
  --table-name $ResultTable `
  --region $AwsRegion `
  --query "Table.{TableStatus:TableStatus,BillingMode:BillingModeSummary.BillingMode,Index:GlobalSecondaryIndexes[?IndexName=='user_id-index']|[0].{Status:IndexStatus,Key:KeySchema,Projection:Projection}}"
```

#### 2. Tạo execution role cho Results Lambda

Role cần:

- `dynamodb:Query` trên `table/<TABLE>/index/user_id-index`.
- `dynamodb:GetItem` và `dynamodb:DeleteItem` trên table.
- `s3:DeleteObject` trên raw và processed bucket.
- CloudWatch Logs write permissions.

Results Lambda không cần `PutItem`; chỉ Inference Lambda ghi kết quả.

#### 3. Đóng gói Results Lambda

```powershell
$ResultsSource = Resolve-Path "D:\kts-smart-agri\frontend-app\backend\lambda\results_handler.py"
$Stage = Join-Path $env:TEMP "kts-results-package"
$Zip = Join-Path $env:TEMP "kts-results.zip"

New-Item -ItemType Directory -Force -Path $Stage | Out-Null
Copy-Item $ResultsSource (Join-Path $Stage "lambda_function.py") -Force
Compress-Archive -Path (Join-Path $Stage "lambda_function.py") -DestinationPath $Zip -Force
tar -tf $Zip
```

#### 4. Tạo Results Lambda

| Thuộc tính | Giá trị |
|---|---|
| Function name | `kts-smartagri-dev-results-lambda` |
| Runtime | Python 3.12 |
| Handler | `lambda_function.lambda_handler` |
| Memory | 256 MB |
| Timeout | 15 giây |
| `RESULTS_TABLE` | result table |
| `USER_INDEX` | `user_id-index` |
| `RAW_IMAGES_BUCKET` | raw bucket |
| `RESULTS_BUCKET` | processed bucket |
| `CORS_ORIGIN` | frontend origin |

#### 5. Tạo API Gateway routes

Sử dụng Cognito Authorizer đã tạo cho tất cả method thật:

| Resource | Method | Authorization | Integration |
|---|---|---|---|
| `/results` | GET | Cognito User Pools | Results Lambda proxy |
| `/results` | OPTIONS | NONE | MOCK |
| `/results/{scanId}` | GET | Cognito User Pools | Results Lambda proxy |
| `/results/{scanId}` | DELETE | Cognito User Pools | Results Lambda proxy |
| `/results/{scanId}` | OPTIONS | NONE | MOCK |

Cho phép API Gateway invoke Lambda với source ARN giới hạn trong REST API và path `/results*`.

#### 6. Cấu hình CORS và deploy

`OPTIONS /results`:

```text
Access-Control-Allow-Methods: GET,OPTIONS
```

`OPTIONS /results/{scanId}`:

```text
Access-Control-Allow-Methods: GET,DELETE,OPTIONS
```

Cả hai cho phép `Authorization,Content-Type`. Thêm CORS headers cho Gateway Responses `DEFAULT_4XX` và `DEFAULT_5XX` để browser đọc được lỗi 401/500.

Sau mọi thay đổi, tạo deployment mới và trỏ stage `dev` đến deployment đó.

#### 7. Kiểm tra ownership

Các test bắt buộc:

1. User A gọi `GET /results` chỉ thấy item có `user_id=A`.
2. User B gọi `GET /results` không thấy item của A.
3. User B xóa scan của A nhận `403` và item vẫn còn.
4. User A xóa scan của A nhận `200`; DynamoDB item, raw object và processed object đều bị xóa.

{{% notice warning %}}
Không dùng DynamoDB `Scan` rồi lọc ở ứng dụng. Query `user_id-index` giảm chi phí và tránh đọc nhầm dữ liệu người dùng khác.
{{% /notice %}}

#### Kết quả triển khai

![Kết quả triển khai - dynamodb table](/images/5-Workshop/5.6-Results-history/dynamodb-table.png)

![Kết quả triển khai - dynamodb user index](/images/5-Workshop/5.6-Results-history/dynamodb-user-index.png)

![Kết quả triển khai - results lambda config](/images/5-Workshop/5.6-Results-history/results-lambda-config.png)

![Kết quả triển khai - api results methods](/images/5-Workshop/5.6-Results-history/api-results-methods.png)

![Kết quả triển khai - api stage deployment](/images/5-Workshop/5.6-Results-history/api-stage-deployment.png)

![Kết quả triển khai - ownership test](/images/5-Workshop/5.6-Results-history/ownership-test.png)
