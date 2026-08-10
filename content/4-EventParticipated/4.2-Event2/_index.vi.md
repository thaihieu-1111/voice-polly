---
title: "Event 2 - Agentic AI Build Week Community Day"
date: 2025-08-13
weight: 2
chapter: false
pre: " <b> 4.2. </b> "
---

# Bài thu hoạch Agentic AI Build Week Community Day

### Thông tin sự kiện

- **Thời gian:** 09:00 ngày 13/08/2025
- **Địa điểm:** Tầng 26, tòa nhà Bitexco, số 02 đường Hải Triều, phường Sài Gòn, Thành phố Hồ Chí Minh
- **Vai trò:** Người tham dự

Sự kiện là dịp các đội chia sẻ sản phẩm, kiến trúc và bài học thực tế sau quá trình tham gia Agentic AI Build Week. Bốn phiên trình bày cho thấy AI agent có thể hỗ trợ thiết kế giải pháp, phân tích tín hiệu doanh nghiệp, vận hành đám đông và xử lý đơn hàng đa kênh.

## 1. Solution Architect Professional AI Native App — Plan V

**Thành viên:** Phạm Tiến Thuận Phát, Huỳnh Hoàng Long, Lê Minh Nghĩa, Trần Đại Vĩ và Nguyễn An.

Nhóm Plan V giải quyết bài toán Solution Architect phải đọc tài liệu yêu cầu, dựng kiến trúc, tạo sơ đồ và ước tính chi phí trong thời gian ngắn.

### Nội dung chính

- Nhận yêu cầu bằng ngôn ngữ tự nhiên hoặc tài liệu dự án có cấu trúc.
- Trích xuất yêu cầu và tạo **Requirements Catalogue** trong vài phút.
- Đề xuất các phương án kiến trúc cấp cao, có khả năng xem xét hybrid cloud và tiêu chuẩn doanh nghiệp.
- Sinh sơ đồ Draw.io có thể chỉnh sửa và sơ đồ dùng AWS Architecture Icons chính thức.
- Tạo ước tính chi phí AWS định hướng cho Region `ap-southeast-1`.
- Cho phép tinh chỉnh kết quả qua chat và custom instruction theo từng dự án.

### Giá trị mang lại

Thay vì bắt đầu từ trang trắng và thực hiện thủ công từng bước, Solution Architect có một bản nháp có căn cứ để kiểm tra, chỉnh sửa và trao đổi với khách hàng. Công cụ cũng có thể hỗ trợ tạo Infrastructure as Code và đưa ước tính chi phí đi cùng kiến trúc.

## 2. Signal Scout — Phát hiện thay đổi chiến lược doanh nghiệp

**Thành viên:** Lê Tấn Lực, Đỗ Hoàng Hiếu, Triệu Quốc Hào, Nguyễn Văn Duy Khiêm, Nguyễn Công Minh và Nguyễn Trần Minh Quân.

Signal Scout thu thập và liên kết các tín hiệu doanh nghiệp rời rạc để phát hiện sớm thay đổi chiến lược, tái cấu trúc và rủi ro.

### Nội dung chính

- Thu thập, xác thực bằng chứng và phân tích các chỉ số tài chính, vận hành.
- Kết nối các tín hiệu thành timeline và câu chuyện có thể kiểm chứng.
- Hỗ trợ các quyết định **Maintain, Adapt hoặc Accelerate** nhưng vẫn giữ con người trong vòng kiểm soát.
- Cung cấp dashboard, báo cáo phân tích và cảnh báo rủi ro cho nhóm chiến lược, quản trị rủi ro và competitive intelligence.
- Kiến trúc sử dụng các dịch vụ như Amazon Bedrock, AgentCore, Amplify, DynamoDB, Lambda, API Gateway, S3, Cognito, CloudWatch và WAF, kết hợp công cụ thu thập dữ liệu bên ngoài.
- Nhóm so sánh nhiều mức tải và tiếp tục tối ưu kiến trúc để giảm chi phí vận hành.

### Bài học

Một hệ thống AI hỗ trợ quyết định chỉ có giá trị khi mỗi kết luận đều gắn với bằng chứng minh bạch. Ngoài độ chính xác của mô hình, kiến trúc cần cân bằng chi phí, khả năng truy vết và quyền quyết định cuối cùng của con người.

## 3. Hackathon Journey và dự án S.H.E.P.H.E.R.D — Team 3KA

**Thành viên:** Huỳnh An Khương, Nguyễn Quốc Huy, Ngô Quang Khôi, Hoàng Lê Thành Đức, Đặng Nguyễn Phước Lộc và Đặng Trường Hưng.

Team 3KA chia sẻ hành trình 24 giờ xây dựng, thất bại, sửa lỗi và hoàn thiện một MVP. Dự án S.H.E.P.H.E.R.D hỗ trợ giám sát mật độ người và phản ứng sớm với tình trạng ùn tắc tại sự kiện.

### Giải pháp

- Phân tích video camera để phát hiện và theo dõi người bằng YOLO và ByteTrack.
- Đo mật độ đám đông, điều kiện hàng chờ và dấu hiệu ùn tắc.
- Dự đoán nguy cơ quá tải, tạo cảnh báo chủ động và đề xuất điều phối nhân sự.
- Kết hợp Amazon SageMaker, Amazon Bedrock AgentCore, Strands Agent và React monitoring dashboard.
- Autonomous Monitor liên tục theo dõi chỉ số; Operator Copilot trả lời câu hỏi ngôn ngữ tự nhiên dựa trên dữ liệu trực tiếp.

### Kinh nghiệm hackathon

- Khó khăn lớn gồm thiếu nền tảng AI/AWS, thời gian ngắn, lỗi mã nguồn, độ trễ inference và duy trì tracking giữa các frame.
- Lần sau cần chuẩn bị mục tiêu rõ ràng, starter toolkit, tài khoản, phân chia vai trò và kế hoạch demo từ sớm.
- Một tính năng nhỏ nhưng chạy hoàn chỉnh có giá trị hơn một ý tưởng lớn chưa thể trình diễn.
- Hackathon không chỉ là cuộc thi; giá trị còn nằm ở kỹ năng hợp tác, khả năng thích nghi và những người đã gặp trong quá trình xây dựng.

## 4. KFC Bot Agent — One Team

**Thành viên:** Anh Duy, Trần Đông, Đoàn Trung, Minh Việt và Anshul Roy.

One Team trình bày conversational ordering agent cho phép khách hàng đặt món ngay trong kênh trò chuyện như Zalo hoặc Messenger mà không phải chuyển ứng dụng hay tạo lại tài khoản.

### Nội dung chính

- Agent hiểu ý định, món ăn, số lượng, tùy chọn, voucher, trạng thái giỏ hàng và lỗi nghiệp vụ.
- Quy trình agent gồm **Goal → Plan → Tools → Act → Verify**; mô hình hiểu yêu cầu còn tool kiểm chứng dữ liệu thực tế.
- Kiến trúc đa kênh dùng adapter cho từng kênh, connector cho từng doanh nghiệp và tool cho từng năng lực mới.
- Hệ thống tìm dữ liệu đáng tin cậy, cập nhật giỏ hàng, áp dụng khuyến mãi và xác nhận lại đơn hàng thật.
- Slide trình bày mức tham chiếu khoảng **0,006 USD/đơn**, **88 USD/tháng**, độ trễ đầu-cuối **3–5 giây** và giảm khoảng **60% mã hạ tầng** nhờ AgentCore.

### Bài học

Conversational ordering không chỉ là chatbot trả lời. Một agent hữu ích phải thực hiện hành động, tuân thủ business rule, kiểm tra trạng thái thật và ngăn sai sót gây thiệt hại tài chính.

## Giá trị nhận được

Qua bốn phiên chia sẻ, tôi học được:

1. Bắt đầu từ vấn đề cụ thể và xác định giá trị người dùng trước khi chọn công nghệ.
2. Thiết kế agent quanh tool, dữ liệu đáng tin cậy và bước xác minh thay vì phụ thuộc hoàn toàn vào câu trả lời của mô hình.
3. Xem chi phí, độ trễ, khả năng quan sát và human control là một phần của kiến trúc ngay từ đầu.
4. Giới hạn phạm vi MVP, phân công vai trò rõ ràng và chuẩn bị demo sớm khi làm việc dưới áp lực thời gian.
5. AI agent có thể hỗ trợ nhiều lĩnh vực, nhưng kết quả phải minh bạch, có thể kiểm chứng và gắn với quy trình nghiệp vụ thực tế.

## Hình ảnh minh chứng tham dự

![Hình ảnh tham dự Agentic AI Build Week Community Day](/images/4-EventParticipated/4.2-Event2/event2-evidence.png)

*Hình ảnh được chụp trực tiếp trong phiên chia sẻ tại sự kiện.*

