---
title: "Tạo Cognito User Pool"
date: 2026-07-18
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

#### 1. Mở Cognito

1. Mở **Amazon Cognito** trong AWS Console.
2. Chọn Region **Asia Pacific (Singapore) - ap-southeast-1**.
3. Chọn **Create user pool**.

#### 2. Cấu hình đăng nhập

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![cognito user pool overview](/images/5-Workshop/5.3-Cognito-auth/cognito-user-pool-overview.png)

![cognito signup settings](/images/5-Workshop/5.3-Cognito-auth/cognito-signup-settings.png)


- Application type: **Single-page application (SPA)**.
- Sign-in identifier: **Email**.
- Required attribute: **Email**.
- Self-registration: **Enabled**.
- Email verification: **Send verification code**.
- MFA: **Optional** hoặc **Off** cho môi trường lab.

Đặt tên:

```text
kts-smartagri-dev-users
```

#### 3. Cấu hình app client

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![cognito app client](/images/5-Workshop/5.3-Cognito-auth/cognito-app-client.png)


Tạo public app client:

```text
kts-smartagri-dev-web
```

- Không tạo client secret cho ứng dụng chạy trong trình duyệt.
- Bật `ALLOW_USER_SRP_AUTH` và `ALLOW_REFRESH_TOKEN_AUTH`.
- Token revocation: bật.

Sau khi tạo, lưu lại:

```text
User Pool ID
App Client ID
```

Không lưu client secret vì SPA không thể bảo vệ secret.

#### 4. Kiểm tra bằng AWS CLI

`GenerateSecret` phải là `false` và `AutoVerifiedAttributes` phải có `email`.
