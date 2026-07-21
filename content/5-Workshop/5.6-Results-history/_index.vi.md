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

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![dynamodb table](/images/5-Workshop/5.6-Results-history/dynamodb-table.png)

![dynamodb user index](/images/5-Workshop/5.6-Results-history/dynamodb-user-index.png)


Trong DynamoDB Console:

1. Mở table `kts-smartagri-dev-inference-results`.
2. Chọn **Indexes** → **Create index**.
3. Partition key: `user_id` kiểu String.
4. Index name: `user_id-index`.
5. Projection: **All**.
6. Chờ table và index cùng chuyển sang `ACTIVE`.

Table dùng `PAY_PER_REQUEST`, vì vậy GSI không có chi phí chạy 24/24 cố định; chi phí phát sinh từ request và storage.

Kiểm tra:

#### 2. Tạo execution role cho Results Lambda

Role cần:

- `dynamodb:Query` trên `table/<TABLE>/index/user_id-index`.
- `dynamodb:GetItem` và `dynamodb:DeleteItem` trên table.
- `s3:DeleteObject` trên raw và processed bucket.
- CloudWatch Logs write permissions.

Results Lambda không cần `PutItem`; chỉ Inference Lambda ghi kết quả.

#### 3. Đóng gói Results Lambda

#### 4. Tạo Results Lambda

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![results lambda config](/images/5-Workshop/5.6-Results-history/results-lambda-config.png)


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

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![api results methods](/images/5-Workshop/5.6-Results-history/api-results-methods.png)


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

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![api stage deployment](/images/5-Workshop/5.6-Results-history/api-stage-deployment.png)


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

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![ownership test](/images/5-Workshop/5.6-Results-history/ownership-test.png)


Các test bắt buộc:

1. User A gọi `GET /results` chỉ thấy item có `user_id=A`.
2. User B gọi `GET /results` không thấy item của A.
3. User B xóa scan của A nhận `403` và item vẫn còn.
4. User A xóa scan của A nhận `200`; DynamoDB item, raw object và processed object đều bị xóa.
