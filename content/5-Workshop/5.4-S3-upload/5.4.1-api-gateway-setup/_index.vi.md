---
title: "Cấu hình API Gateway & Lambda"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

Để chuẩn bị cho luồng tải ảnh an toàn, hệ thống KTs Smart Agriculture sẽ không đẩy luồng dữ liệu hình ảnh trực tiếp qua máy chủ. Thay vào đó, chúng ta sẽ xây dựng cơ chế **Pre-signed URL** (Đường dẫn ủy quyền tạm thời).

Cơ chế này yêu cầu hai thành phần chính:

- **Hàm AWS Lambda:** Đảm nhiệm việc giao tiếp với Amazon S3 để tạo ra một đường dẫn `PUT` có giới hạn thời gian.
- **Amazon API Gateway:** Đóng vai trò là cửa ngõ giao tiếp để Frontend gọi hàm Lambda trên.

![Luồng Pre-signed URL](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-upload/presign-flow.png)

#### Bước 1: Tạo Lambda Function cấp URL

1. Truy cập vào [AWS Lambda Console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1) và chọn **Create function**.
2. Đặt tên hàm là `kts-smartagri-presign-handler`, chọn Runtime là **Python 3.10** (hoặc mới hơn).
3. Đảm bảo Execution Role của Lambda đã được đính kèm Policy cấp quyền `s3:PutObject` vào bucket `kts-smartagri-dev-raw-images`.
4. Viết mã nguồn Python sử dụng thư viện `boto3` để sinh URL với hàm `generate_presigned_url`. Nhấn **Deploy** để lưu mã nguồn.

![Cấu hình Lambda](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-upload/lambda-presign.png)

#### Bước 2: Cấu hình Amazon API Gateway

Thay vì để Frontend gọi thẳng vào Lambda, chúng ta dùng API Gateway để quản lý lưu lượng và bảo mật (tích hợp Cognito ở bước sau).

1. Truy cập vào **Amazon API Gateway Console**, chọn tạo một **REST API** mới.
2. Tại giao diện quản lý API, chọn **Create Resource** và đặt tên đường dẫn là `/presign`.

![Tạo Resource](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-upload/api-resource.png)

3. Chọn resource `/presign` vừa tạo, nhấp vào **Create Method** và chọn phương thức **POST**.
4. Ở phần Integration type, chọn **Lambda Function** và nhập tên hàm `kts-smartagri-presign-handler` vừa tạo ở Bước 1. Nhấn Save.

![Kết nối Lambda](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-upload/api-integration.png)

#### Bước 3: Triển khai API (Deploy)

1. Nhấp vào nút **Deploy API** trên thanh công cụ.
2. Tạo một Stage mới tên là `dev` và tiến hành triển khai.
3. Sau khi triển khai xong, hệ thống sẽ cung cấp cho bạn một **Invoke URL** (Đường dẫn kích hoạt). Hãy copy đường dẫn này để chuẩn bị khai báo vào file `config.js` của ứng dụng Frontend.

![Lấy Invoke URL](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-upload/api-deploy.png)
