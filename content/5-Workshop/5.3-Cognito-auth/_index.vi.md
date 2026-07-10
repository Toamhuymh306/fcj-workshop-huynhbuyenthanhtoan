---
title: "Xác thực người dùng (Cognito)"
date: 2026-07-10
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

#### Sử dụng Amazon Cognito User Pool

Trong phần này, bạn sẽ cấu hình và demo cơ chế bảo mật của nền tảng KTs Smart Agriculture sử dụng **Amazon Cognito**.

Để bảo vệ các API phân tích AI và ngăn chặn việc tải ảnh rác lên hệ thống S3, ứng dụng bắt buộc người dùng (nông dân, chuyên gia nông nghiệp) phải đăng nhập. Sau khi xác thực thành công, Cognito sẽ cấp một thẻ ủy quyền (JWT Token). Web App sẽ sử dụng Token này đính kèm vào phần Header của mọi yêu cầu gửi lên máy chủ AWS.

![Cognito Auth Flow](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/diagram2.png)

#### Nội dung

- [Cấu hình Cognito User Pool](5.3.1-user-pool-setup/)
- [Demo Đăng nhập & Nhận Token](5.3.2-login-demo/)
