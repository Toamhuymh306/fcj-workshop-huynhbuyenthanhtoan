---
title: "Demo Đăng nhập & Nhận Token"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 5.3.2. </b> "
---

#### Khởi chạy Frontend Web App

1. Mở Terminal hoặc Command Prompt tại thư mục chứa source code Frontend của KTs Smart Agriculture.
2. Chạy một local web server (Ví dụ dùng Node.js: gõ `npx http-server` hoặc dùng extension Live Server trên VS Code).

![Khởi chạy Web](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/run-server.png)

3. Mở trình duyệt và truy cập vào địa chỉ `http://localhost:8080` (hoặc port tương ứng). Giao diện trang chủ của ứng dụng sẽ hiện ra.

![Giao diện Web](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/web-home.png)

#### Đăng ký và Đăng nhập (Sign up & Sign in)

1. Tại giao diện web, chọn nút **Đăng ký (Sign Up)**.
2. Nhập một địa chỉ email hợp lệ và mật khẩu theo đúng policy đã thiết lập ở bài trước. Nhấn Đăng ký.
3. Chuyển sang form **Đăng nhập (Sign In)**, nhập lại thông tin vừa tạo và tiến hành đăng nhập vào hệ thống.

![Đăng nhập](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/web-login.png)

#### Kiểm tra JWT Token trong trình duyệt

Mục đích cốt lõi của bước này là xác nhận Amazon Cognito đã trả về một vé ủy quyền (Token) hợp lệ cho Frontend.

1. Ngay trên trình duyệt đang mở Web App, nhấn phím **F12** để mở công cụ Developer Tools.
2. Chuyển sang tab **Console** hoặc **Application** (phần Local Storage).
3. Bạn sẽ nhìn thấy một chuỗi ký tự mã hóa rất dài bắt đầu bằng `eyJ...`. Đây chính là **ID Token** và **Access Token** do Cognito cấp.

![Token Console](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.3-Cognito-auth/console-token.png)

{{% notice info %}}
Token này đóng vai trò như một "tấm thẻ thông hành". Bất cứ khi nào bạn nhấn nút tải ảnh lên, đoạn mã Javascript trong file `app.js` sẽ tự động đính kèm Token này vào Header của HTTP Request để chứng minh danh tính với máy chủ AWS.
{{% /notice %}}

#### Tổng kết phần 5.3

Chúc mừng nhóm KTs đã thiết lập và tích hợp thành công luồng xác thực người dùng. Bức tường bảo mật đầu tiên đã được dựng lên vững chắc, ngăn chặn hoàn toàn các truy cập ẩn danh.

Hệ thống giờ đây đã sẵn sàng cho luồng tải dữ liệu thực tế. Để chuẩn bị cho bước kiểm chứng AI quan trọng tiếp theo, hãy chuẩn bị sẵn bức ảnh chụp lá cacao bị nhện đỏ tấn công (chứ không phải bệnh nấm VSD hay thán thư) để xem hệ thống phân loại có trả về kết quả chính xác hay không.
