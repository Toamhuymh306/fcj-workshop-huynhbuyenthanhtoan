---
title: "Các bài blogs đã đăng"
date: 2024-01-01
weight: 3
chapter: false
pre: " <b> 3. </b> "
---

Dưới đây là 3 bài blog tiêu biểu mình đã thực hiện trong kỳ thực tập, tập trung vào kiến trúc cloud-native, bảo mật cho microservices và hạ tầng huấn luyện AI Agent trên AWS.

### [Blog 1 - AMAZON EKS AUTO MODE & ISTIO AMBIENT MESH – GIẢI PHÁP BETTER TOGETHER CHO MICROSERVICES](3.1-Blog1/)

Blog này giới thiệu sự kết hợp giữa EKS Auto Mode và Istio Ambient Mesh nhằm giải quyết hai bài toán lớn của microservices: vận hành hạ tầng và bảo mật. Bài viết phân tích cách EKS Auto Mode tự động hóa quản lý node (provisioning, scaling với Karpenter) kết hợp kiến trúc không cần sidecar của Istio Ambient Mesh (ztunnel và HBONE) để cung cấp mTLS. Đây là giải pháp giúp doanh nghiệp giảm đáng kể gánh nặng vận hành, tối ưu chi phí nhưng vẫn bảo đảm an toàn dữ liệu từ L4 đến L7.

### [Blog 2 - LÀM SAO ĐỂ HUẤN LUYỆN MỘT AI AGENT THÔNG MINH MÀ KHÔNG BỊ NÓ QUA MẶT](3.2-Blog2/)

Blog này chia sẻ kinh nghiệm thực chiến khi huấn luyện Multi-turn Reinforcement Learning cho AI Agent. Bài viết đi sâu vào 3 bài học cốt lõi: (1) xây dựng Sandbox cô lập hoàn toàn để tránh rủi ro trên hệ thống thực, (2) thiết kế Dense Reward kết hợp đánh giá độc lập để chống Reward Hacking, và (3) kiểm soát chặt trajectory cùng Turn Budget để tránh lãng phí token và giúp mô hình học hiệu quả từ kết quả cuối cùng.

### [Blog 3 - XÂY DỰNG HẠ TẦNG MULTI-TURN RL CHO AI AGENT VỚI AMAZON SAGEMAKER HYPERPOD](3.3-Blog3/)

Blog này trình bày kiến trúc event-driven trên AWS cho bài toán huấn luyện AI Agent ra quyết định qua nhiều lượt. Bài viết mô tả cấu trúc 3 lớp: Amazon SageMaker HyperPod (trên EKS) để sinh câu trả lời và cập nhật trọng số, AWS Fargate làm môi trường chấm điểm (Reward Environment), và Amazon Nova Forge SDK để điều phối. Điểm nhấn là chiến lược triển khai 2 giai đoạn giúp tự động cấp phát GPU khi có dữ liệu upload lên S3 và tự động giải phóng khi hoàn thành, từ đó tối ưu chi phí vận hành.
