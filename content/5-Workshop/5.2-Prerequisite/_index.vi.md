---
title : "Các bước chuẩn bị"
date : 2026-07-10
weight : 2
chapter : false
pre : " <b> 5.2. </b> "
---

#### Tài khoản và Quyền truy cập

Cần có một tài khoản AWS đang hoạt động với IAM user có đủ quyền để triển khai SAM stack, quản lý Lambda, S3, DynamoDB, Cognito, API Gateway, SQS, SNS, ECR, CloudFront, WAFv2, CloudTrail và CloudWatch. Để lấy khóa bảo mật kết nối từ máy trạm, thực hiện các bước sau:

**Bước 1 (Trên AWS Console):** Đăng nhập vào AWS Management Console, điều hướng đến **IAM (Identity and Access Management)** → **Users**, chọn tên người dùng của bạn (ví dụ: `kts-agri-dev`), chuyển sang tab **Security credentials** và tìm mục **Access keys**. Nhấn **Create access key**, chọn trường hợp sử dụng **Command Line Interface (CLI)**, xác nhận điều khoản và hoàn tất tạo để nhận được cặp Access Key ID và Secret Access Key. Tải xuống tệp `.csv` để lưu trữ các khóa bảo mật này một cách an toàn.

![create iam](/fcj-workshop-huynhbuyenthanhtoan/images/5-Workshop/5.2-Prerequisite/create_iam.png)

**Bước 2 (Trên máy trạm):** Mở Terminal hoặc PowerShell trên máy tính cá nhân và chạy lệnh `aws configure`. Nhập Access Key ID và Secret Access Key vừa tạo ở Bước 1, đặt **Default region name** là `ap-southeast-1` (Singapore) và **Default output format** là `json`. Cấu hình này được tự động lưu trong thư mục home của người dùng (`%USERPROFILE%\.aws\` trên Windows hoặc `~/.aws/` trên Linux/macOS).

Xác minh cấu hình đã đúng bằng lệnh:
```bash
aws sts get-caller-identity
```
Lệnh này sẽ trả về Account ID và ARN của IAM user mà không có lỗi xác thực.

---

#### Môi trường máy trạm cục bộ

Đảm bảo máy trạm đã cài đặt đầy đủ các công cụ sau trước khi triển khai:

- **AWS CLI v2** — dùng để tương tác với các dịch vụ AWS và triển khai WAF stack trực tiếp qua CloudFormation.
- **SAM CLI** — dùng để build và deploy serverless backend (Lambda functions, API Gateway, DynamoDB và các tài nguyên liên quan).
- **Docker Desktop** — bắt buộc để `sam build` có thể xây dựng Docker image cho AI Inference Lambda chứa PyTorch và trọng số mô hình ResNet50 + LeNet. Docker Desktop phải đang **chạy** tại thời điểm build.
- **Python 3.11+** — được SAM CLI yêu cầu để cài đặt các dependencies của Lambda trong quá trình build.
- **Node.js v20+** và **npm** — cần thiết để cài đặt dependencies và build ứng dụng React frontend.

Môi trường cục bộ yêu cầu kết nối Internet ổn định để tải các gói pip (torch, torchvision, boto3), Docker base image từ `public.ecr.aws`, và các gói npm trong quá trình build.

---

#### Lựa chọn Region

Hệ thống này được triển khai trên hai AWS region do yêu cầu của CloudFront:

- **WAF WebACL** phải được triển khai tại **us-east-1 (N. Virginia)** vì CloudFront chỉ chấp nhận WAF WebACL với scope `CLOUDFRONT` được tạo tại us-east-1.
- Tất cả các tài nguyên backend còn lại (Lambda, API Gateway, DynamoDB, S3, Cognito, SQS, SNS, ECR, CloudTrail) được triển khai tại **ap-southeast-1 (Singapore)** nhằm tối ưu độ trễ kết nối cho người dùng cuối tại thị trường Đông Nam Á và đảm bảo tính tương thích của tất cả các dịch vụ AWS được sử dụng trong kiến trúc.
