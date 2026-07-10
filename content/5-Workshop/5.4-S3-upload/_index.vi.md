---
title: "Kiến trúc: Tải ảnh Serverless"
date: 2024-01-01
weight: 4
chapter: false
pre: " <b> 5.4 </b> "
---

#### Luồng thu thập dữ liệu an toàn

Phần này phân tích chi tiết luồng kiến trúc serverless được thiết kế để tiếp nhận hình ảnh đầu vào một cách bảo mật. Đây là nền tảng quan trọng nhằm chuẩn bị dữ liệu thô cho các tác vụ phân tích thị giác máy tính (computer vision) ở giai đoạn sau. Việc áp dụng cơ chế Pre-signed URL giúp giảm thiểu băng thông cho hệ thống backend và tối ưu hóa thời gian tải file.

**Chi tiết các bước thực thi:**

1. **Xác thực danh tính:** Người dùng tiến hành đăng nhập thông qua **Amazon Cognito** để nhận JSON Web Token (JWT), đảm bảo chỉ những tài khoản hợp lệ mới có quyền truy cập.
2. **Định tuyến & Bảo mật biên (Edge):** Client gửi yêu cầu cấp quyền tải ảnh qua giao thức HTTPS. Request này đi qua mạng lưới toàn cầu của **Amazon CloudFront** và được bảo vệ chặt chẽ bởi tường lửa **AWS WAF**.
3. **Kiểm soát quyền truy cập:** **Amazon API Gateway** tiếp nhận request và xác minh tính hợp lệ của JWT token với Cognito.
4. **Khởi tạo Pre-signed URL:** Sau khi xác thực thành công, API Gateway sẽ kích hoạt hàm `AWS Lambda Presign`. Function này đóng vai trò giao tiếp an toàn với Amazon S3 để tạo ra một đường dẫn tải lên (Pre-signed URL) có giới hạn thời gian.
5. **Upload trực tiếp lên S3:** Đường dẫn này được trả về cho client ở frontend (Bước 5a & 5b). Cuối cùng, ứng dụng sẽ dùng chính URL này để đẩy ảnh trực tiếp vào **Raw Images S3 Bucket** (Bước 6) mà không cần phải trung chuyển qua server backend.

Cơ chế thu thập dữ liệu tách rời (decoupled) này giúp lưu trữ an toàn các file ảnh gốc, đồng thời sẵn sàng kích hoạt các luồng sự kiện cho mô hình xử lý AI ngay sau đó.

![Architecture Flow](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-upload/diagram1_2.png)
