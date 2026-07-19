---
title: "Blog 3"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.3. </b> "
---

# XÂY DỰNG HẠ TẦNG MULTI-TURN RL CHO AI AGENT VỚI AMAZON SAGEMAKER HYPERPOD

Khi AI Agent phải xử lý các tác vụ nhiều bước, Reinforcement Learning truyền thống không còn đủ. Đây là cách AWS xây dựng hạ tầng Multi-turn RL trên Amazon SageMaker HyperPod.

Khi bạn xây dựng AI Agent để xử lý quy trình nhiều bước như gọi API, truy vấn cơ sở dữ liệu và xử lý lỗi hệ thống, các phương pháp huấn luyện truyền thống thường tối ưu từng phản hồi riêng lẻ sẽ bộc lộ giới hạn. Chất lượng của một bước đi phụ thuộc vào kết quả của nhiều bước sau đó.

Để giải quyết bài toán này, AWS giới thiệu kiến trúc Multi-turn Reinforcement Learning cho mô hình Amazon Nova chạy trên Amazon SageMaker HyperPod.

## Giải pháp hoạt động như thế nào

Hệ thống được thiết kế theo mô hình event-driven pipeline với 3 lớp xử lý cốt lõi:

- Amazon SageMaker HyperPod (trên EKS) để sinh phản hồi và cập nhật trọng số mô hình.
- AWS Fargate làm Reward Environment để chấm điểm quỹ đạo.
- Amazon Nova Forge SDK để điều phối toàn bộ pipeline.

## Điểm cốt lõi, mô hình triển khai 2 giai đoạn

Giai đoạn 1 (cố định): triển khai một lần bằng AWS CDK để dựng sẵn hạ tầng nền tảng như VPC, EKS, ECS, S3, Step Functions.

Giai đoạn 2 (tạm thời theo từng lần chạy): khi upload file .jsonl lên S3, hệ thống tự kích hoạt Step Functions để cấp phát GPU phục vụ huấn luyện; khi hoàn thành sẽ tự động giải phóng tài nguyên.

## Kết quả mang lại

- Tự động hóa toàn bộ pipeline: từ cấp phát hạ tầng, điều phối môi trường đánh giá đến huấn luyện.
- Theo dõi trực quan qua Step Functions và debug luồng qua SQS.
- Tối ưu chi phí nhờ cấp phát tài nguyên theo nhu cầu thực tế, giảm thời gian GPU idle.

Tóm lại, đây là một kiến trúc tham khảo đáng chú ý cho Multi-turn RL trên AWS, đặc biệt phù hợp với workload cần tài nguyên tính toán lớn và thay đổi theo từng đợt dữ liệu.

## Hướng dẫn thực hành

1. Dùng AWS CDK triển khai hạ tầng nền (VPC, EKS, ECS, S3, Step Functions) một lần.
2. Chuẩn bị dữ liệu huấn luyện theo định dạng .jsonl và upload vào S3 bucket đầu vào.
3. Cấu hình trigger để Step Functions tự động chạy khi có object mới trong S3.
4. Theo dõi từng stage qua Step Functions; kiểm tra reward flow và queue depth qua SQS metrics.
5. Sau khi job kết thúc, xác nhận tài nguyên GPU đã được teardown để tránh phát sinh chi phí.

## Link bài viết

- [Xem bài Blog 3 trên AWS](https://aws.amazon.com/blogs/machine-learning/deploying-multi-turn-rl-infrastructure-for-amazon-nova-on-amazon-sagemaker-hyperpod/)

## Hình ảnh bài viết

![Ảnh Blog 3](/images/3-BlogsPosted/blog3.jpg)
