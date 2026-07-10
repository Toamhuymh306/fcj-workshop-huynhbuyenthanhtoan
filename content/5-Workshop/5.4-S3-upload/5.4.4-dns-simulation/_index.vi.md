---
title: "Mô phỏng DNS On-premises"
date: 2024-01-01
weight: 4
chapter: false
pre: " <b> 5.4.4 </b> "
---

#### Ngữ cảnh Hệ thống

Các AWS PrivateLink endpoint hoạt động dựa trên các Elastic Network Interface (ENI) được phân bổ tại nhiều Availability Zone (AZ). Dù các ENI này giữ một địa chỉ IP cố định trong suốt vòng đời của chúng, AWS luôn khuyến nghị sử dụng DNS để phân giải tên miền. Phương pháp này đảm bảo rằng các ứng dụng luôn được định tuyến đến các dải IP mới nhất và chính xác nhất, đặc biệt khi hệ thống mở rộng sang các AZ mới hoặc có sự thay đổi về hạ tầng mạng.

Trong phần này, chúng ta sẽ thiết lập một cơ chế mô phỏng mạng nội bộ (on-premises). Mục tiêu là tạo ra một quy tắc định tuyến (forwarding rule) để đẩy các truy vấn DNS từ mạng mô phỏng này sang một Private Hosted Zone trên Route 53.

#### 1. Cấu hình DNS Alias Record cho Interface Endpoint

Bước đầu tiên là ánh xạ endpoint với một tên miền nội bộ trong VPC.

1. Truy cập vào [Route 53 - Hosted Zones](https://us-east-1.console.aws.amazon.com/route53/v2/hostedzones?region=us-east-1#). Tìm và chọn Private Hosted Zone mang tên `s3.us-east-1.amazonaws.com` (tài nguyên này đã được khởi tạo tự động bằng CloudFormation trước đó).

![hosted zone](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/hosted-zone.png)

2. Tiến hành tạo bản ghi (record) bằng cách nhấn **Create record** với các thông số sau:
   - **Record name & type:** Giữ nguyên mặc định (Type A).
   - **Alias:** Bật tính năng này.
   - **Route traffic to:** Chọn **Alias to VPC Endpoint**.
   - **Region:** Chọn **US East (N. Virginia) [us-east-1]**.
   - **Choose endpoint:** Dán chuỗi Regional VPC Endpoint DNS name mà bạn đã lưu lại ở bài 4.3.

![record1](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/record1.png)

3. Tiếp tục nhấn **Add another record** để tạo thêm một bản ghi wildcard đại diện với cấu hình định tuyến tương tự:
   - **Record name:** `*`
   - **Record type:** Type A.
   - **Alias:** Bật.
   - **Route traffic to:** Alias to VPC Endpoint.
   - **Region:** US East (N. Virginia) [us-east-1].
   - **Choose endpoint:** Dán chuỗi Regional VPC Endpoint DNS name.

   Nhấn **Create records** để lưu đồng thời cả hai bản ghi.

![record 2](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/record2.png)
![result](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/result.png)

#### 2. Thiết lập Resolver Forwarding Rule

Route 53 Resolver Forwarding Rules đóng vai trò như một trạm trung chuyển, cho phép đẩy các truy vấn DNS từ VPC hiện tại sang các máy chủ DNS khác (ví dụ: máy chủ tại on-premises). Để thiết lập mô hình "conditional forwarder" mô phỏng on-premises, ta sẽ tạo một rule có nhiệm vụ bắt các truy vấn DNS gửi tới S3 và chuyển tiếp chúng vào Private Hosted Zone thuộc "VPC Cloud".

1. Từ menu của Route 53, chọn **Inbound endpoints**.
2. Nhấn vào ID của inbound endpoint đang hoạt động.

![Inbound endpoint](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/route53-1.png)

3. Sao chép lại 2 địa chỉ IP hiển thị trên màn hình.

![Ip addresses](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/route53-2.png)

4. Chuyển sang mục **Resolver** > **Rules**, nhấn **Create rule**.

![Ip addresses](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/route53-3.png)

5. Khai báo các thông số cho rule:
   - **Name:** `myS3Rule`
   - **Rule type:** Forward
   - **Domain name:** `s3.us-east-1.amazonaws.com`
   - **VPC:** Chọn `VPC On-prem`
   - **Outbound endpoint:** Chọn `VPCOnpremOutboundEndpoint`
   - **Target IP Addresses:** Nhập 2 địa chỉ IP của inbound endpoint vừa lưu ở bước trên.

   Nhấn **Submit** để hoàn tất.

![create rule](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/route53-4.png)
![create rule](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/route53-5.png)
![create rule](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/route53-6.png)
![create rule](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/route53-7.png)

#### 3. Kiểm thử Mô hình

Giờ là lúc xác minh xem kết nối từ môi trường on-premises mô phỏng có thực sự được phân giải DNS và định tuyến thành công tới S3 qua Interface Endpoint hay không.

1. Kết nối vào máy chủ `Test-Interface-Endpoint` EC2 thông qua **Session Manager**.

![create rule](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.4-S3-onprem/test1.png)

2. Sử dụng lệnh `dig` để kiểm tra quá trình phân giải DNS:
   ```bash
   dig +short s3.us-east-1.amazonaws.com
   ```
