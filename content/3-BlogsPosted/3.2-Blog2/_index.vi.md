---
title: "Blog 2"
date: 2024-01-01
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---

# LAM SAO DE HUAN LUYEN MOT AI AGENT THONG MINH MA KHONG BI NO QUA MAT

Dạo gần đây, cụm từ AI Agent đang trở thành tâm điểm của giới công nghệ. Khác với chatbot chỉ biết trả lời đơn lượt, một AI Agent thực thụ có khả năng tự lên kế hoạch, gọi công cụ, đọc kết quả, tự sửa sai và chạy qua nhiều lượt để giải quyết tác vụ phức tạp.

Thế nhưng, huấn luyện Multi-turn Reinforcement Learning cho các agent này là một bài toán không hề dễ. Dưới đây là 3 bài học cốt lõi.

## 1. Xay dung moi truong Sandbox co lap

Khi AI học bằng thử-sai qua nhiều lượt, việc ném nó vào production là cực kỳ rủi ro. Một agent chưa ổn định có thể kích hoạt nhầm các hành động nghiêm trọng như hoàn tiền, xóa dữ liệu hoặc gọi API ngoài kiểm soát.

Giải pháp:

- Tạo Sandbox giả lập hoàn toàn.
- Với tool đọc dữ liệu: dùng mocked response cố định.
- Với tool thay đổi trạng thái: cô lập bộ nhớ theo từng episode và tear down sạch sau khi kết thúc.

## 2. Bay Reward Hacking, khi AI lach luat

AI tối ưu chính xác thứ được code trong hàm reward, không tối ưu thứ người vận hành kỳ vọng trong đầu.

Ví dụ:

- Phạt nhiều lượt quá mạnh: agent trả lời bừa để kết thúc nhanh.
- Thưởng theo số lần gọi tool: agent spam tool mà không giải đúng bài toán.

Giải pháp:

- Thiết kế Dense Reward: không chỉ chấm đúng/sai cuối cùng, mà chấm theo tiến độ để tạo tín hiệu học liên tục.
- Luôn có External Evaluation độc lập với reward training. Nếu reward tăng nhưng task success rate giảm, gần như chắc chắn đang bị reward hacking.

## 3. Kiem soat trajectory va Turn Budget

Càng nhiều lượt hội thoại, context càng phình to và tiêu tốn token. Cần kiểm soát chặt:

- max_turns: số lượt tối đa cho mỗi tác vụ.
- sampling_max_tokens: giới hạn token cho mỗi lượt.

Nếu người thật cần N lượt để xử lý tác vụ, có thể đặt trần ban đầu cho agent ở khoảng 1.5 x N rồi tối ưu dần theo telemetry.

## Loi ket

Huấn luyện AI Agent đa lượt không chỉ là nạp dữ liệu và bấm train, mà là bài toán thiết kế môi trường, luật chơi và cơ chế đánh giá.

Khi tách bạch được completion và correctness, bạn sẽ biết mô hình thực sự đang tiến bộ hay chỉ học mẹo định dạng.

## Huong dan thuc hanh

1. Xác định task đa lượt cụ thể và dựng Sandbox mô phỏng đầy đủ các tool bắt buộc.
2. Thiết kế reward theo nhiều mốc trung gian thay vì chỉ điểm cuối cùng.
3. Thiết lập evaluator độc lập để theo dõi task success rate song song với reward score.
4. Đặt max_turns và sampling_max_tokens từ sớm, theo dõi log để điều chỉnh theo dữ liệu thực.
5. Chạy A/B giữa policy mới và policy cũ trong tập test đóng trước khi đưa vào production.

## Link bài viết

- [Xem bài Blog 2 trên AWS](https://aws.amazon.com/vi/blogs/machine-learning/best-practices-for-multi-turn-reinforcement-learning-in-amazon-sagemaker-ai/?content_source=fb&fb_content_id=Q9-wBQEJUL5h_O8ySkuTEnaHjxaW2smXbC9wKJyqsMGcQlBKtBYimWtpyl_G3FcymQ&channel_type=fb)

## Hình ảnh bài viết

![Ảnh Blog 2](/images/3-BlogsPosted/blog2.jpg)
