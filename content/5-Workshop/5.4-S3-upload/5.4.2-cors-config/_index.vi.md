---
title: "Cấu hình CORS"
date: 2026-07-18
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

#### S3 CORS

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![s3 cors](/images/5-Workshop/5.4-S3-upload/s3-cors.png)


Tạo file cấu hình:

Trong production, thay localhost bằng domain CloudFront/frontend thật. Không dùng `*` khi không cần thiết.

#### API Gateway CORS

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![api presign options](/images/5-Workshop/5.4-S3-upload/api-presign-options.png)


`OPTIONS /presign` phải trả về:

```text
Access-Control-Allow-Origin: <frontend-origin>
Access-Control-Allow-Headers: Authorization,Content-Type
Access-Control-Allow-Methods: POST,OPTIONS
```

Presign Lambda cũng phải trả các header CORS tương ứng trong response thật.

#### Kiểm tra preflight

Thực hiện bước này trên AWS Management Console, kiểm tra kỹ tên tài nguyên, Region và các giá trị cấu hình trước khi lưu. Sau khi hoàn tất, đối chiếu màn hình với hình bên dưới để chắc chắn tài nguyên đã được tạo đúng và đang ở trạng thái sẵn sàng.

![presign preflight](/images/5-Workshop/5.4-S3-upload/presign-preflight.png)


Kỳ vọng `HTTP/1.1 200 OK` và đủ ba header CORS.
