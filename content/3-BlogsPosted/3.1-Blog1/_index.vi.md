---
title: "Blog 1"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

# AMAZON EKS AUTO MODE & ISTIO AMBIENT MESH - GIAI PHAP BETTER TOGETHER CHO MICROSERVICES

Trong thế giới công nghệ hiện đại, khi kiến trúc microservices phát triển từ vài dịch vụ nhỏ thành hàng trăm dịch vụ, hai thách thức lớn thường xuất hiện:

- Quản lý hạ tầng tính toán sao cho ổn định, tự động và ít tốn công sức.
- Bảo mật giao tiếp giữa các dịch vụ để đảm bảo an toàn dữ liệu và giảm rủi ro.

Amazon đã giới thiệu sự kết hợp giữa EKS Auto Mode và Istio Ambient Mesh, một cặp đôi ăn ý giúp tự động hóa hạ tầng và bảo mật service-to-service bằng mTLS. Đây là bước tiến quan trọng trong việc giảm tải vận hành và nâng cao bảo mật cho hệ thống microservices.

## Amazon EKS Auto Mode - Tu dong hoa ha tang Kubernetes

- Quản lý vòng đời node: tự động provisioning, scaling, patching và update.
- Hệ điều hành Bottlerocket: Linux tối giản, bất biến, tối ưu cho container.
- Thành phần hệ thống tích hợp sẵn: VPC CNI, kube-proxy, EBS CSI driver, CoreDNS... được AWS quản lý trực tiếp.
- Karpenter-powered scaling: tự động chọn instance phù hợp, tối ưu chi phí bằng cách thay thế node dư thừa hoặc chuyển sang Spot Instance.

Điểm mạnh: người dùng chỉ cần tập trung vào workload, không phải lo lắng về node hay add-ons phức tạp.

## Istio Ambient Mesh - Bao mat va quan ly traffic khong can sidecar

- ztunnel (Zero-trust tunnel): proxy L3/L4 chạy trên mỗi node, cung cấp mTLS, chính sách L4 và telemetry TCP.
- Waypoint Proxy: proxy L7 tùy chọn, hỗ trợ routing, retries, circuit breaking và chính sách HTTP-aware.
- HBONE Protocol: cho phép traffic mTLS đi qua hạ tầng mạng hiện có mà không cần sidecar.

Điểm mạnh: giảm overhead tài nguyên, loại bỏ sidecar, nhưng vẫn đảm bảo bảo mật và khả năng quan sát.

## Loi ich khi ket hop EKS Auto Mode va Istio Ambient Mesh

- Giảm gánh nặng vận hành: AWS lo phần compute, Istio lo phần networking.
- Bảo mật tích hợp: mTLS tự động, chính sách linh hoạt từ L4 đến L7.
- Tối ưu chi phí: scaling thông minh, giảm node dư thừa.
- Triển khai dễ dàng: Terraform và Helm giúp setup nhanh chóng.

## Ket luan

Sự kết hợp giữa Amazon EKS Auto Mode và Istio Ambient Mesh mang lại một môi trường Kubernetes hiện đại, bảo mật và ít tốn công sức vận hành. Đây là giải pháp lý tưởng cho cả doanh nghiệp mới bắt đầu với microservices lẫn các dự án hiện đại hóa hệ thống.

Nếu bạn đang tìm cách giảm tải công việc vận hành, tăng cường bảo mật và tối ưu chi phí cho hệ thống microservices, hãy cân nhắc thử nghiệm bộ đôi này.

## Huong dan thuc hanh

1. Tạo EKS cluster mới và bật EKS Auto Mode để AWS tự quản lý node lifecycle.
2. Cài Istio Ambient Mesh theo ambient profile, kiểm tra ztunnel đã chạy trên toàn bộ node.
3. Bật mTLS strict cho namespace thử nghiệm và xác minh traffic nội bộ được mã hóa.
4. Nếu cần policy nâng cao L7, triển khai thêm Waypoint Proxy cho từng service quan trọng.
5. Theo dõi chi phí và hiệu năng qua CloudWatch cùng Karpenter events để tối ưu instance types.

## Link bài viết

- [Xem bài Blog 1 trên AWS](https://aws.amazon.com/blogs/containers/better-together-amazon-eks-auto-mode-and-istio-ambient-mesh/?content_source=fb&fb_content_id=Q9-wBQGZ6UvpkDkfhsO3gB1bF7c6ufwtvYep5SR9fVCMX9vZbeotl4YCoV1PtCN8ZA&channel_type=fb)

## Hình ảnh bài viết

![Ảnh Blog 1](/fcj-workshop-huynhbuyenthanhtoan/images/3-BlogsPosted/blog1.jpg)
