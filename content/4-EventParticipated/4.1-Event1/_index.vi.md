---
title: "Event 1 - AWS Cloud, Monitoring và Security Agent"
date: 2025-08-13
weight: 1
chapter: false
pre: " <b> 4.1. </b> "
---

# Bài thu hoạch sự kiện AWS Cloud, Monitoring và Security Agent

### Thông tin sự kiện

- **Thời gian:** 09:00 ngày 13/08/2025
- **Địa điểm:** Tầng 26, tòa nhà Bitexco, số 02 đường Hải Triều, phường Sài Gòn, Thành phố Hồ Chí Minh
- **Vai trò:** Người tham dự

Sự kiện gồm ba phiên chia sẻ, tập trung vào kiến thức nền tảng AWS, cách giám sát dịch vụ dựa trên trải nghiệm người dùng và xu hướng ứng dụng AI agent vào bảo mật ứng dụng web.

## 1. Inside the Exam: AWS Cloud Practitioner

**Diễn giả:** Ngô Lê Tấn Huy

Phiên đầu tiên trình bày cấu trúc và phương pháp chuẩn bị cho kỳ thi AWS Certified Cloud Practitioner (CLF-C02). Nội dung giúp người mới hiểu phạm vi kiến thức của chứng chỉ nền tảng và xây dựng kế hoạch học tập phù hợp.

### Nội dung chính

- Bài thi gồm **65 câu hỏi**, thời gian **90 phút** và điểm đạt là **700/1.000**.
- Bốn nhóm kiến thức gồm Cloud Concepts; Security and Compliance; Cloud Technology and Services; Billing, Pricing and Support.
- Phần Cloud Concepts tập trung vào lợi ích của AWS Cloud, AWS Well-Architected Framework và AWS Cloud Adoption Framework.
- Phần Security and Compliance nhấn mạnh Shared Responsibility Model, IAM, bảo mật hạ tầng và tuân thủ.
- Phần Cloud Technology and Services giới thiệu hạ tầng toàn cầu, compute, storage, database và networking.
- Phần Billing, Pricing and Support trình bày mô hình giá EC2, công cụ quản lý chi phí và các gói hỗ trợ AWS.

### Kinh nghiệm ôn thi

- Học dịch vụ theo **use case** và từ khóa thay vì chỉ ghi nhớ định nghĩa.
- Làm đề thử, phân tích kỹ câu trả lời sai và nhận biết các từ khóa quyết định như “least cost” hoặc “most scalable”.
- Thực hành với AWS Free Tier để hiểu rõ hơn EC2, S3, IAM và các dịch vụ cốt lõi.
- Dùng AWS Skill Builder, đề thi thử và tài liệu học phù hợp để củng cố kiến thức.

## 2. SLA and Monitoring: From SLA to Monitoring What Really Matters

**Diễn giả:** Nguyễn Huỳnh Sơn

Phiên thứ hai giải thích vai trò của Service Level Agreement (SLA) và chỉ ra rằng hạ tầng hoạt động bình thường chưa chắc đồng nghĩa với trải nghiệm người dùng tốt.

### Nội dung chính

- SLA xác định mức dịch vụ mà khách hàng có thể kỳ vọng và là cơ sở cho trách nhiệm, quản trị rủi ro và đo lường hiệu năng.
- Monitoring cần bắt đầu từ **customer journey** và business metrics, sau đó mới đi xuống application, infrastructure và AWS services.
- Dashboard chỉ hiển thị CPU, memory hoặc HTTP 200 có thể vẫn “xanh” dù người dùng không đăng nhập hoặc hoàn thành giao dịch được.
- Một endpoint `/api` có thể trả về `200 OK`, trong khi endpoint `/login` thất bại do lỗi kết nối cơ sở dữ liệu.
- Quy trình cảnh báo nên đi từ metric quan trọng, CloudWatch Alarm, SNS topic đến email hoặc Slack để đội vận hành phản ứng trước khi khách hàng khiếu nại.

### Bài học rút ra

- **Healthy infrastructure không đồng nghĩa với healthy user experience.**
- Cần biết SLA thực sự bảo đảm phần nào và phần nào thuộc trách nhiệm của đội phát triển, vận hành.
- Nên theo dõi những hành động người dùng quan trọng như đăng nhập, tìm kiếm, thanh toán hoặc checkout thay vì chỉ theo dõi tài nguyên.
- Thiết kế hệ thống với tư duy sự cố luôn có thể xảy ra: lập kế hoạch cho failure và tránh để một lỗi đơn lẻ làm ngừng toàn bộ dịch vụ.

## 3. Securing Your Web Apps with AWS Security Agent

**Diễn giả:** Thịnh Nguyễn

Phiên cuối giới thiệu AWS Security Agent như một frontier agent hỗ trợ tự động hóa các hoạt động bảo mật trong toàn bộ vòng đời phát triển phần mềm.

### Nội dung chính

- Kiểm thử xâm nhập thủ công thường mất nhiều thời gian, chi phí cao và kết quả phụ thuộc vào kinh nghiệm của pentester.
- Security Agent có khả năng lập kế hoạch, suy luận và thực hiện tác vụ bảo mật với mức can thiệp hạn chế của con người.
- Agent hỗ trợ ba nhóm công việc: **design review**, **code review** và **automated penetration testing**.
- Design Security Review có thể kiểm tra tài liệu Markdown hoặc Terraform theo các managed pack như PCI DSS, NIST CSF và AWS Well-Architected.
- Code Security Review tích hợp với pull request GitHub/GitLab, nhận xét trực tiếp trên mã nguồn và đề xuất bản vá.
- Automated Pentesting có thể xác thực như người dùng thật, kiểm tra chuỗi khai thác nhiều bước và cung cấp bằng chứng có thể kiểm chứng.
- Mô hình chi phí được trình bày theo task-hour, bên cạnh free tier cho design review, code review và thời gian dùng thử agent.

### Giới hạn cần lưu ý

- Các cơ chế xác thực như MFA, biometrics hoặc mTLS có thể chặn quá trình kiểm thử tự động.
- Agent khó phát hiện lỗi logic nghiệp vụ nếu thiếu bối cảnh chuyên sâu.
- Task-hour có thể tích lũy nhanh, vì vậy cần theo dõi mức sử dụng và chi phí.
- AI agent hỗ trợ mở rộng năng lực bảo mật nhưng không thay thế hoàn toàn chuyên gia và quy trình kiểm soát của con người.

## Giá trị nhận được

Qua ba phiên chia sẻ, tôi kết nối được kiến thức từ nền tảng đến vận hành và bảo mật:

1. Xây dựng nền tảng AWS có hệ thống thông qua lộ trình Cloud Practitioner.
2. Chuyển tư duy monitoring từ tài nguyên sang trải nghiệm và hành trình thực tế của người dùng.
3. Hiểu cách AI agent có thể hỗ trợ review kiến trúc, mã nguồn và kiểm thử bảo mật.
4. Nhận thức rõ hơn rằng một hệ thống tốt cần đồng thời đáp ứng kiến thức cloud, khả năng quan sát và bảo mật xuyên suốt vòng đời.

## Hình ảnh minh chứng tham dự

![Hình ảnh tham dự sự kiện AWS Cloud, Monitoring và Security Agent](/images/4-EventParticipated/4.1-Event1/event1-evidence.jpg)

*Hình ảnh được chụp trực tiếp tại địa điểm tổ chức sự kiện.*

