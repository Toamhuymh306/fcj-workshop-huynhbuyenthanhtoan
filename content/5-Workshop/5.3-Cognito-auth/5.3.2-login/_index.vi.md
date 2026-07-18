---
title: "Đăng ký, xác minh email và đăng nhập"
date: 2026-07-18
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

#### 1. Cấu hình frontend

Mở file cấu hình frontend và cập nhật Region, User Pool ID, App Client ID và API endpoint:

```javascript
const AWS_CONFIG = {
  region: "ap-southeast-1",
  cognito: {
    userPoolId: "<USER_POOL_ID>",
    clientId: "<APP_CLIENT_ID>"
  },
  apiGateway: {
    endpoint: "https://<REST_API_ID>.execute-api.ap-southeast-1.amazonaws.com/dev"
  }
};
```

#### 2. Chạy frontend local

Mở `http://127.0.0.1:8000`.

#### 3. Đăng ký và xác minh

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![cognito confirm email](/images/5-Workshop/5.3-Cognito-auth/cognito-confirm-email.png)

![cognito login success](/images/5-Workshop/5.3-Cognito-auth/cognito-login-success.png)


1. Chọn **Đăng ký**.
2. Nhập email và mật khẩu đáp ứng password policy.
3. Lấy mã xác minh trong email.
4. Nhập mã trên form xác nhận.
5. Đăng nhập sau khi tài khoản chuyển sang trạng thái `CONFIRMED`.

Ứng dụng cũng phải hỗ trợ **Gửi lại mã** khi mã cũ hết hạn hoặc email đến chậm.

#### 4. Kiểm tra token an toàn

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![cognito token claims](/images/5-Workshop/5.3-Cognito-auth/cognito-token-claims.png)


Trong DevTools Console:

```javascript
(() => {
  const token = localStorage.getItem("idToken");
  if (!token) return { error: "idToken not found" };
  const payload = JSON.parse(atob(token.split(".")[1].replace(/-/g, "+").replace(/_/g, "/")));
  return {
    token_use: payload.token_use,
    issuer: payload.iss,
    audience: payload.aud,
    expires_at: new Date(payload.exp * 1000).toISOString()
  };
})()
```

Kết quả phải có `token_use: "id"` và issuer trỏ đến User Pool vừa tạo.

{{% notice warning %}}
REST API Cognito Authorizer của workshop nhận JWT thô trong header `Authorization`. Không thêm tiền tố `Bearer` khi gọi các route đã cấu hình.
{{% /notice %}}
