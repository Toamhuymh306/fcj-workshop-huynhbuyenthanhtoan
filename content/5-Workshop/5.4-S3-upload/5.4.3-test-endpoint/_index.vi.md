---
title: "Tải ảnh lên S3"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 5.4.3 </b> "
---

#### Tải ảnh lên qua Giao diện Web

Trong bước này, chúng ta sẽ kiểm tra tính năng tải ảnh. Theo như sơ đồ kiến trúc, client (frontend) sẽ gọi qua API Gateway & Lambda để lấy một Pre-signed URL, sau đó sử dụng URL này để tải ảnh trực tiếp lên bucket S3 một cách bảo mật.

1. Mở trình duyệt web và truy cập vào đường dẫn frontend của ứng dụng (domain CloudFront của bạn).
2. Đăng nhập bằng tài khoản người dùng Cognito (nếu chưa đăng nhập).
3. Tại giao diện upload, chọn một bức ảnh mẫu từ máy tính của bạn và nhấn nút **Upload**.
4. Đợi thông báo thành công xác nhận quá trình tải ảnh thông qua Pre-signed URL đã hoàn tất.

![upload-ui](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-upload/upload-ui.png)

#### Kiểm tra Object trong S3 Bucket

Bây giờ, chúng ta sẽ kiểm tra xem ảnh đã được lưu thành công vào đúng bucket trên S3 hay chưa.

1. Truy cập vào AWS Management Console và đi đến **S3 console**.
2. Ở menu bên trái, chọn **Buckets**.
3. Nhấn vào tên bucket **Raw Images** của bạn (bucket được cấu hình để chứa ảnh thô).
4. Bạn sẽ thấy file ảnh vừa tải lên xuất hiện trong danh sách các objects.

![check bucket](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-upload/check-bucket.png)
