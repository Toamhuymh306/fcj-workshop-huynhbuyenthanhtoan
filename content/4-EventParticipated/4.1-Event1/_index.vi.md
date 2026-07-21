---
title: "Event 1"
date: 2026-05-23
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# Bài thu hoạch FCAJ Community Day

### Thông tin sự kiện

- **Tên sự kiện:** FCAJ Community Day
- **Thời gian:** 09:00 - 12:00, Thứ Bảy, ngày 23/05/2026
- **Địa điểm:** Tầng 26, Bitexco Financial Tower, Thành phố Hồ Chí Minh
- **Hình thức tham gia:** Trực tiếp
- **Vai trò:** Người tham dự

### Mục đích của sự kiện

- Kết nối cộng đồng công nghệ và chia sẻ kinh nghiệm triển khai hệ thống trong môi trường doanh nghiệp.
- Cập nhật xu hướng Platform Engineering và cách sử dụng AI hiệu quả dựa trên ngữ cảnh nghiệp vụ.
- Tìm hiểu phương pháp xây dựng hệ thống multi-agent an toàn, đáng tin cậy và có khả năng mở rộng.
- Hiểu cách Amazon CloudFront hỗ trợ tối ưu hiệu năng, chi phí, bảo mật và tính sẵn sàng của ứng dụng.
- Học hỏi kinh nghiệm phát triển sản phẩm trong hackathon và các nguyên nhân khiến kết quả của LLM không hoàn toàn nhất quán.

### Danh sách diễn giả và nhóm chia sẻ

- **Tinh Truong** - Platform Engineer tại GoTymeX
- **Anh Pham** - AWS Community Builder, Cloud Consultant tại G-AsiaPacific Vietnam
- **Thinh Nguyen** - DevOps Engineer tại First Cloud AI Journey
- **Team VIB** - Nhóm tham gia LotusHacks 2026
- **Duc Dao** - Solutions Architect tại Cloud Kinetics
- **Vy Lam** - Senior Business Systems Analyst tại VPBank

### Nội dung nổi bật

#### Enterprise Multi-Agent System cho đánh giá tín dụng startup

Phiên chia sẻ trình bày cách tiếp cận bài toán đánh giá tín dụng startup, một nhóm khách hàng thường thiếu báo cáo tài chính nhiều năm và tài sản thế chấp như mô hình tín dụng truyền thống yêu cầu. Thay vào đó, hệ thống cần xem xét thêm tài sản trí tuệ, đội ngũ sáng lập, sản phẩm, thị trường và các chỉ số như TAM, SAM, SOM.

- Chọn **single-agent** cho quy trình đơn giản, một miền nghiệp vụ, ít công cụ và quyết định có rủi ro thấp.
- Chọn **multi-agent** khi dữ liệu đa dạng và cần nhiều chuyên môn như phân tích tài chính, đánh giá đội ngũ và đánh giá rủi ro.
- Mỗi agent cần có vai trò, mục tiêu, ngữ cảnh và phạm vi quyền hạn rõ ràng.
- Hệ thống cần hỗ trợ xử lý song song, audit trail, mở rộng độc lập và fault tolerance.
- Con người vẫn phải tham gia phê duyệt các quyết định quan trọng; AI chỉ hỗ trợ phân tích, không thay thế trách nhiệm cuối cùng.

#### Bảo mật và quản trị AI trong doanh nghiệp

Một bản POC có thể được tạo nhanh, nhưng để đưa AI từ thử nghiệm lên production cần thêm nhiều lớp kiểm soát:

- Giới hạn attack surface bằng thiết kế mạng, Security Group, network ACL, subnet và private connectivity phù hợp.
- Quản lý danh tính, xác thực và phân quyền theo nguyên tắc least privilege.
- Phòng chống prompt injection và kiểm soát dữ liệu đầu vào, đầu ra của mô hình.
- Bảo vệ, luân chuyển access key và API key; không lưu thông tin bí mật trong mã nguồn.
- Ghi log và trace các quyết định của LLM để phục vụ kiểm toán và xử lý sự cố.
- Triển khai theo lộ trình: **Build Core → Internal Testing/SIT → UAT → Pilot → Scale**.

Phiên chia sẻ cũng nhấn mạnh việc chuẩn hóa kết nối công cụ thông qua **Model Context Protocol (MCP)** phải đi kèm kiểm soát quyền truy cập, bởi một kết nối thuận tiện cũng có thể làm tăng phạm vi rủi ro nếu cấu hình không đúng.

#### Platform Engineering trong kỷ nguyên AI

Platform Engineering tạo một lớp nền tảng dùng chung để đội phát triển có thể tự phục vụ tài nguyên và quy trình triển khai, thay vì mỗi developer phải tự quản lý toàn bộ hạ tầng.

- Chuẩn hóa quy trình build, deploy, vận hành và giám sát.
- Giảm tải nhận thức cho developer và giúp họ tập trung vào giá trị sản phẩm.
- Kết hợp các vai trò Platform Engineer, DevOps và SRE để nâng cao độ tin cậy.
- Sử dụng AI hiệu quả bằng cách cung cấp ngữ cảnh cụ thể về ngành, người dùng và mục tiêu, thay vì đặt câu hỏi chung chung.
- Duy trì nền tảng Software Engineering, Cloud, CI/CD, Infrastructure as Code và kỹ năng sản phẩm; GenAI là công cụ hỗ trợ chứ không thay thế kiến thức cốt lõi.

#### Tối ưu và bảo vệ nội dung với Amazon CloudFront

Phiên CloudFront giúp tôi hiểu CDN không chỉ dùng để tăng tốc website mà còn hỗ trợ tối ưu chi phí, bảo mật và khả năng phục hồi:

- Cache nội dung tại edge location và Regional Edge Cache để giảm request tới origin.
- Sử dụng AWS WAF, giới hạn địa lý, header tùy chỉnh và chính sách truy cập origin để hạn chế truy cập trái phép.
- Dùng Origin Failover và trang lỗi tùy chỉnh để tăng tính sẵn sàng.
- Tận dụng HTTP/3, TLS session reuse và CloudFront Functions để cải thiện trải nghiệm người dùng.
- Theo dõi traffic và lựa chọn mô hình giá phù hợp với nhu cầu thực tế.

#### Hành trình xây dựng sản phẩm tại LotusHacks 2026

Team VIB chia sẻ quá trình phát triển một công cụ tạo và chỉnh sửa giao diện bằng AI trong giới hạn 36 giờ. Nhóm phân chia tính năng, ghép các thành phần thành sản phẩm hoàn chỉnh, ưu tiên demo và liên tục thu hẹp phạm vi để kịp thời hạn.

Những bài học đáng chú ý là tránh **over-feature**, tập trung vào giá trị cốt lõi, chuẩn bị kịch bản demo rõ ràng, chủ động xin hỗ trợ khi gặp bế tắc và thu thập phản hồi từ người dùng thử nghiệm.

#### Vì sao kết quả của LLM không hoàn toàn nhất quán?

Ngay cả khi `temperature = 0`, đầu ra trên nền tảng cloud vẫn có thể khác nhau do sai số floating-point, thứ tự tính toán song song, cơ chế sampling và các tối ưu hạ tầng của nhà cung cấp.

- Không nên giả định LLM là một hàm xác định tuyệt đối.
- Prompt cần rõ ràng, có cấu trúc và quy định chặt chẽ định dạng đầu ra.
- Cần kiểm thử nhiều lần với các tình huống thực tế và ghi log đầy đủ.
- Có thể cân nhắc self-host khi yêu cầu kiểm soát, bảo mật hoặc tính tái lập rất cao.
- Hệ thống production phải có validation, retry, fallback và human review phù hợp với mức độ rủi ro.

### Những gì học được

#### Tư duy thiết kế hệ thống

- Luôn bắt đầu từ người dùng, bài toán nghiệp vụ và giá trị cần tạo ra trước khi chọn công nghệ.
- Phân tách agent theo chuyên môn và thiết kế ranh giới trách nhiệm rõ ràng.
- Xem khả năng mở rộng, độ tin cậy, bảo mật và auditability là yêu cầu ngay từ đầu.

#### Kiến thức kỹ thuật

- Hiểu rõ hơn vai trò của MCP, IAM, network isolation, logging và tracing trong hệ thống AI doanh nghiệp.
- Biết cách CloudFront kết hợp caching, WAF, origin protection và failover để bảo vệ frontend.
- Nhận thức được tính xác suất của LLM và sự cần thiết của validation, testing, retry và fallback.

#### Kỹ năng phát triển sản phẩm

- Một demo tốt cần giải quyết đúng vấn đề cốt lõi thay vì có quá nhiều tính năng.
- Phải biết trình bày sản phẩm, giải thích giá trị và tiếp nhận phản hồi người dùng.
- Nền tảng Software Engineering và Cloud vẫn là điều kiện quan trọng để đưa GenAI vào production.

### Ứng dụng vào đồ án KTS Smart Agri

- Duy trì kiến trúc bất đồng bộ bằng Amazon S3, SQS và Lambda để các thành phần có thể mở rộng và xử lý lỗi độc lập.
- Áp dụng Cognito Authorizer, IAM least privilege và quyền sở hữu dữ liệu theo từng người dùng.
- Sử dụng CloudFront để phân phối frontend qua HTTPS, giảm tải cho origin và tăng cường bảo mật.
- Ghi log trên CloudWatch, theo dõi lỗi inference và thiết kế retry/fallback cho pipeline AI.
- Kiểm tra loại ảnh đầu vào bằng Amazon Rekognition trước khi gọi mô hình nhằm giảm dữ liệu không hợp lệ.
- Giữ human-in-the-loop: kết quả nhận diện bệnh là thông tin hỗ trợ và cần được xác minh trước khi đưa ra quyết định quan trọng.

### Trải nghiệm tại sự kiện

Sự kiện giúp tôi nhìn rõ khoảng cách giữa một mô hình AI chạy được trong bản demo và một hệ thống đủ an toàn để vận hành trong doanh nghiệp. Các phiên chia sẻ bổ trợ cho nhau: Platform Engineering cung cấp nền tảng vận hành; CloudFront giải quyết hiệu năng và bảo vệ lớp phân phối; multi-agent mở rộng khả năng xử lý nghiệp vụ; còn phiên LLM giải thích vì sao hệ thống AI luôn cần kiểm thử và cơ chế kiểm soát.

Từ kinh nghiệm của Team VIB, tôi cũng nhận ra giới hạn thời gian không chỉ là khó khăn mà còn buộc nhóm xác định đúng tính năng cốt lõi. Đây là bài học trực tiếp cho đồ án KTS Smart Agri: hoàn thiện luồng end-to-end ổn định, bảo mật và có thể trình diễn trước khi mở rộng thêm chức năng.

### Hình ảnh sự kiện

![Ảnh tham gia FCAJ Community Day](/images/4-EventParticipated/event1.jpg)

> Tổng thể, FCAJ Community Day mang lại cho tôi cả kiến thức kiến trúc, kinh nghiệm triển khai production và tư duy phát triển sản phẩm. Bài học quan trọng nhất là một hệ thống AI tốt không chỉ tạo được kết quả, mà còn phải an toàn, đáng tin cậy, có thể kiểm toán và thực sự hữu ích cho người dùng.
