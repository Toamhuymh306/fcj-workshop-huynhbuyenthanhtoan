---
title: "Cấu hình Cognito User Pool"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 5.3.1. </b> "
---

1. Truy cập vào [Amazon Cognito Console](https://ap-southeast-1.console.aws.amazon.com/cognito/home?region=ap-southeast-1).
2. Nhấp vào nút **Create user pool** (Tạo nhóm người dùng).

{{% notice note %}}
Cognito User Pool đóng vai trò như một cơ sở dữ liệu quản lý danh tính. Nó sẽ giúp dự án KTs Smart Agriculture quản lý việc đăng ký, đăng nhập và cấp quyền (thông qua JWT) cho người dùng một cách an toàn.
{{% /notice %}}

![cognito](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/cognito-home.png)

3. Trong bảng điều khiển tạo User Pool, thực hiện các cấu hình sau:

- **Bước 1 (Sign-in experience):** Chọn **Email** làm phương thức đăng nhập chính.
- **Bước 2 (Security requirements):** Thiết lập chính sách mật khẩu (Password policy). Có thể chọn _No MFA_ (Không cần xác thực 2 bước) để quá trình demo web diễn ra nhanh gọn hơn.

![cognito](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/cognito-signin.png)

- **Bước 3 & Bước 4:** Giữ nguyên các thiết lập mặc định cho trải nghiệm đăng ký (Sign-up experience) và gửi email xác nhận.
- **Bước 5 (Integrate your app):** + Đặt tên cho User Pool là `kts-smart-agri-user-pool-prod`.
  - Đánh dấu chọn tạo **App client**. Đặt tên cho App client là `KTs-Web-Client`.
  - Đảm bảo chọn **Public client** và **Không tạo Client secret** (Do Frontend của chúng ta là Vanilla JS/HTML chạy trên trình duyệt, không bảo mật được secret key).

![cognito](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/cognito-app-client.png)

- Bấm **Next** để xem lại toàn bộ cấu hình.
- Cuối cùng, bấm **Create user pool**. Sau khi tạo thành công, hãy copy lại **User Pool ID** và **Client ID** để chuẩn bị điền vào file `config.js` ở Frontend.

![cognito](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/cognito-complete.png)
