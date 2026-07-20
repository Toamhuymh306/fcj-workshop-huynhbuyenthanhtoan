---
title: "Event 2"
date: 2026-06-06
weight: 1
chapter: false
pre: " <b> 4.2. </b> "
---

{{% notice warning %}}
**Lưu ý:** Các thông tin dưới đây chỉ nhằm mục đích tham khảo, vui lòng **không sao chép nguyên văn** cho bài báo cáo của bạn, kể cả cảnh báo này.
{{% /notice %}}

# Bài thu hoạch FCAJ Technical Meetup

### Thông tin sự kiện

- **Tên sự kiện:** First Cloud AI Journey (FCAJ) Technical Meetup
- **Thời gian:** 09:00 - 12:00, Thứ Bảy, ngày 06/06/2026
- **Địa điểm:** Bitexco Financial Tower, Thành phố Hồ Chí Minh
- **Hình thức tham gia:** Trực tiếp
- **Vai trò:** Người tham dự

### Mục đích của sự kiện

- Chia sẻ kiến thức thực tế về Cloud, an ninh mạng, AI/ML và phát triển ứng dụng hiện đại.
- Giới thiệu cách kết hợp AWS WAF với ML-NIDS để nâng cao khả năng phát hiện tấn công.
- Minh họa kiến trúc serverless WebSocket cho game multiplayer và GraphRAG trên AWS.
- Cung cấp kiến thức nền tảng về Docker, lộ trình nghề nghiệp IT và kỹ năng làm việc nhóm.
- Giúp người tham dự kết nối kiến thức kỹ thuật với bài toán triển khai thực tế.

### Danh sách chủ đề và người trình bày

- **Lê Hoàng Gia Đại** - AWS WAF và Machine Learning NIDS
- **Nguyễn Quốc Bảo** - Kết nối Godot với AWS WebSocket
- **Việt Phát** - GraphRAG với Amazon Bedrock và Amazon Neptune
- **Bảo** - Containerization với Docker
- **Huy Phước** - Xây dựng hiệu quả làm việc nhóm
- **Vĩnh Trần** - Hành trình từ IT Helpdesk đến Senior System Administrator

### Nội dung nổi bật

#### Tăng cường phát hiện tấn công với AWS WAF và ML-NIDS

AWS WAF là lớp bảo vệ đầu tiên cho website, API Gateway, Application Load Balancer và CloudFront trước SQL injection, XSS, bot độc hại, brute-force và scraping. Tuy nhiên, rule tĩnh chủ yếu nhận diện mẫu tấn công đã biết nên khó phát hiện zero-day hoặc hành vi bất thường mới.

Machine Learning NIDS bổ sung khả năng học từ lưu lượng mạng và phát hiện bất thường. Quy trình được trình bày gồm:

- Thu thập và làm sạch bộ dữ liệu CSE-CIC-IDS2018.
- Xử lý missing value, giá trị không hợp lệ và loại bỏ đặc trưng không cần thiết.
- Cân bằng dữ liệu để cải thiện khả năng nhận diện các lớp tấn công thiểu số.
- Huấn luyện và đánh giá mô hình bằng precision, recall, F1-score và confusion matrix.
- Triển khai kiến trúc với VPC, EC2, ALB, AWS WAF, S3, Kinesis Data Firehose và Lambda.
- Tích hợp Security Hub, GuardDuty, CloudWatch và SNS để giám sát, tổng hợp cảnh báo và phản ứng sự cố.

Bài học quan trọng là WAF dựa trên chữ ký và ML phát hiện bất thường nên được dùng bổ trợ lẫn nhau. Chất lượng dữ liệu, giám sát thời gian thực và cập nhật mô hình liên tục quyết định hiệu quả của hệ thống.

#### Kết nối Godot với AWS WebSocket cho game multiplayer

API Gateway WebSocket duy trì kết nối hai chiều giữa Godot client và backend. Route key như `$request.body.action` định tuyến message đến Lambda; DynamoDB lưu `connectionId`, trạng thái ghép trận, đối thủ, lựa chọn và thời điểm tạo phiên.

- Godot sử dụng `WebSocketPeer`, `connect_to_url()` và cơ chế polling để quản lý kết nối.
- Message được tuần tự hóa thành JSON và gửi bằng `send_text()`.
- Lambda xử lý tìm đối thủ, ghép trận, ghi lựa chọn, tính kết quả và gửi Win/Lose/Draw cho hai client.
- Các trạng thái `waiting_for_opponent`, `match_found`, `result` và `opponent_disconnected` giúp client cập nhật giao diện.
- Cần xử lý stale connection, TTL, truy vấn DynamoDB hiệu quả và tính stateless của Lambda.

Việc chọn API Gateway WebSocket + Lambda hay dịch vụ game server chuyên dụng phụ thuộc vào tần suất đồng bộ, mô hình game state, matchmaking và quy mô người chơi.

#### Xây dựng GraphRAG với Amazon Bedrock và Amazon Neptune

RAG đưa tri thức bên ngoài vào prompt tại thời điểm truy vấn để câu trả lời bám sát dữ liệu chuyên ngành. GraphRAG mở rộng phương pháp này bằng cách biểu diễn thực thể và quan hệ dưới dạng graph, nhờ đó hỗ trợ multi-hop reasoning.

- **Hướng managed:** Amazon Bedrock Knowledge Bases hỗ trợ chunking, trích xuất thực thể và embedding; Neptune Analytics lưu trữ, xây dựng và phân tích quan hệ graph.
- **Hướng custom:** LlamaIndex kiểm soát pipeline tạo knowledge graph; Amazon Neptune lưu graph và hỗ trợ truy vấn bằng Cypher.

Hướng managed giảm công vận hành, còn hướng custom phù hợp khi cần kiểm soát sâu cách xử lý dữ liệu, ontology và truy vấn.

#### Containerization với Docker

Khác với máy ảo phải đóng gói cả hệ điều hành, container chia sẻ kernel của host nên nhẹ, khởi động nhanh và tiêu thụ ít tài nguyên hơn. Docker hiện thực hóa nguyên tắc **build once, run anywhere** bằng cách đóng gói ứng dụng và dependency thành image.

- Dockerfile mô tả từng layer của image.
- Layer cache giúp tăng tốc quá trình build.
- Container tạo môi trường nhất quán giữa development, testing và production.
- Docker phù hợp cho CI/CD, microservices, cloud-native application và hiện đại hóa hệ thống legacy.

#### Từ IT Helpdesk đến Senior System Administrator

Lộ trình nghề nghiệp bắt đầu từ khả năng xử lý sự cố, giao tiếp với người dùng và hiểu cách hệ thống vận hành. Để chuyển sang System Administrator cần học sâu Linux, networking, security patching, monitoring và capacity planning; đồng thời chủ động xây dựng lab thực hành.

Khi tiếp tục sang Cloud và DevOps, tư duy chuyển từ thao tác thủ công sang elastic scaling, managed services, Infrastructure as Code, version control, automation và CI/CD. Kinh nghiệm thực tế, khả năng phân tích sự cố và portfolio dự án có giá trị lớn trong phỏng vấn.

#### Nghệ thuật làm việc nhóm hiệu quả

Trello, ClickUp, Google Workspace, Slack và Discord giúp quản lý công việc và giao tiếp, nhưng công cụ không thay thế được trách nhiệm cá nhân. Nhóm hiệu quả cần mục tiêu chung, phân công rõ ràng, cập nhật tiến độ minh bạch và hỗ trợ nhau khi gặp trở ngại.

### Những gì học được

#### Kiến thức kỹ thuật

- Hiểu mô hình phòng thủ nhiều lớp khi kết hợp AWS WAF, ML-NIDS, logging và cảnh báo.
- Nắm được luồng serverless WebSocket từ Godot đến API Gateway, Lambda và DynamoDB.
- Phân biệt RAG với GraphRAG và hai hướng triển khai managed/custom trên AWS.
- Hiểu image, container, Dockerfile, layer cache và vai trò của Docker trong CI/CD.

#### Tư duy nghề nghiệp và làm việc nhóm

- Nền tảng Linux, networking và troubleshooting rất quan trọng trước khi đi sâu vào Cloud/DevOps.
- Học tập hiệu quả cần tập trung vào năng lực cốt lõi và xây dựng dự án thực tế.
- Tài liệu hóa, tự động hóa và không thử nghiệm trực tiếp trên production là thói quen quan trọng.
- Giao tiếp rõ ràng và trách nhiệm với phần việc quyết định hiệu quả của nhóm.

### Ứng dụng vào đồ án KTS Smart Agri

- Dùng AWS WAF trước CloudFront/API Gateway để lọc các request phổ biến có dấu hiệu tấn công.
- Kết hợp CloudWatch Logs, metrics, alarms và SNS để theo dõi pipeline inference.
- Áp dụng tư duy phát hiện bất thường cho việc giám sát upload hoặc tần suất gọi API.
- Tiếp tục đóng gói Lambda AI bằng Docker image để đảm bảo môi trường inference nhất quán.
- Tổ chức dữ liệu chẩn đoán theo người dùng; trong tương lai có thể nghiên cứu GraphRAG cho tri thức bệnh cây và quan hệ triệu chứng.
- Quản lý công việc nhóm bằng backlog, người phụ trách, thời hạn và cập nhật tiến độ thường xuyên.

### Trải nghiệm tại sự kiện

Sự kiện tạo ra một hành trình kiến thức liền mạch từ bảo vệ hệ thống, xây dựng ứng dụng realtime và khai thác dữ liệu bằng AI đến đóng gói phần mềm và phát triển nghề nghiệp. Tôi đặc biệt ấn tượng với việc kết hợp AWS WAF và ML-NIDS: một lớp xử lý mối đe dọa đã biết, lớp còn lại tìm kiếm hành vi bất thường.

Phiên GraphRAG mở ra hướng phát triển mới cho KTS Smart Agri, trong khi Docker và WebSocket cung cấp các kỹ thuật có thể áp dụng ngay vào triển khai. Hai phiên về nghề nghiệp và teamwork nhắc tôi rằng sản phẩm tốt không chỉ đến từ công nghệ mà còn từ kỷ luật, giao tiếp và khả năng phối hợp.

> Tổng thể, Event 2 giúp tôi kết nối kiến thức Cloud, Security, AI, Backend và DevOps thành một góc nhìn hệ thống. Bài học lớn nhất là mỗi công nghệ chỉ phát huy hiệu quả khi được đặt đúng vị trí trong kiến trúc và được vận hành bởi một đội ngũ có quy trình rõ ràng.
