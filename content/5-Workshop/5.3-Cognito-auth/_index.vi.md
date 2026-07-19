---
title: "Xác thực người dùng với Cognito"
date: 2026-07-18
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Mục tiêu

Trong phần này, bạn tạo Amazon Cognito User Pool, bật xác minh email và kết nối frontend với app client. ID token do Cognito phát hành sẽ được API Gateway Cognito Authorizer kiểm tra trước khi cho phép gọi Presign API và Results API.

![Cognito Auth Flow](/images/5-Workshop/5.3-Cognito-auth/diagram2.png)

Luồng xác thực:

1. Người dùng đăng ký bằng email và mật khẩu.
2. Cognito gửi mã xác minh đến email.
3. Người dùng xác nhận tài khoản và đăng nhập.
4. Frontend lưu ID token trong phiên người dùng.
5. API Gateway xác minh issuer, audience, chữ ký và thời hạn token.

{{% notice warning %}}
Không ghi ID token vào tài liệu hoặc ảnh chụp. Token cho phép gọi API trong thời gian còn hiệu lực.
{{% /notice %}}

#### Nội dung

1. [Tạo Cognito User Pool](5.3.1-user-pool-setup/)
2. [Đăng ký, xác minh email và đăng nhập](5.3.2-login/)
