---
title: "Tự đánh giá"
date: 2026-08-01
weight: 6
chapter: false
pre: " <b> 6. </b> "
---

# Tự đánh giá quá trình tham gia First Cloud AI Journey

Trong thời gian tham gia chương trình **Workforce Bootcamp – First Cloud AI Journey** tại **Công ty TNHH Amazon Web Services Việt Nam**, từ ngày **01/06/2026 đến ngày 15/08/2026**, tôi đã có cơ hội hệ thống hóa kiến thức cloud, thực hành triển khai dịch vụ AWS và rèn luyện phương pháp tự học trong một dự án thực tế.

Dự án chính của tôi là **Polly Voice**, ứng dụng web serverless cung cấp hai chức năng: chuyển văn bản thành giọng nói bằng **Amazon Polly** và chuyển giọng nói thành văn bản bằng **Amazon Transcribe**. Trong quá trình thực hiện, tôi đã xây dựng giao diện React, backend Node.js, cơ chế xác thực bằng Amazon Cognito, REST API với API Gateway và Lambda, lưu media trên Amazon S3, lưu lịch sử trong DynamoDB và triển khai frontend với AWS Amplify.

Bên cạnh dự án, tôi còn thực hiện worklog, proposal, tài liệu workshop song ngữ, viết blog và tham gia các sự kiện chuyên môn về AWS Cloud, monitoring, security và agentic AI. Những hoạt động này giúp tôi cải thiện đồng thời kỹ năng kỹ thuật, viết tài liệu, trình bày và tổng hợp kiến thức.

## Kết quả đạt được

- Hiểu rõ hơn cách thiết kế một hệ thống **AWS Serverless** theo luồng hoàn chỉnh từ frontend, xác thực, API, xử lý nghiệp vụ đến lưu trữ.
- Biết tích hợp Amazon Polly và Amazon Transcribe vào ứng dụng thực tế.
- Áp dụng presigned URL để upload/download tệp trực tiếp với Amazon S3, giảm tải cho API Gateway và Lambda.
- Thực hành quản lý quyền truy cập theo nguyên tắc **least privilege**, bảo vệ API bằng Cognito JWT và giữ S3 ở chế độ private.
- Biết dùng Infrastructure as Code và quy trình build/deploy để triển khai, kiểm tra và cập nhật hệ thống.
- Cải thiện khả năng đọc tài liệu kỹ thuật, phân tích lỗi, ghi chép worklog và trình bày nội dung bằng tiếng Việt và tiếng Anh.
- Mở rộng góc nhìn qua các chủ đề Cloud Practitioner, SLA/monitoring, AWS Security Agent và các dự án Agentic AI Build Week.

## Bảng tự đánh giá

| STT | Tiêu chí | Minh chứng từ quá trình thực tập | Tốt | Khá | Cần cải thiện |
| ---: | --- | --- | :---: | :---: | :---: |
| 1 | **Kiến thức AWS** | Hiểu và sử dụng Amplify, Cognito, API Gateway, Lambda, Polly, Transcribe, S3 và DynamoDB | ✅ | ☐ | ☐ |
| 2 | **Kỹ năng phát triển phần mềm** | Xây dựng và kết nối frontend React với backend Node.js trên kiến trúc serverless | ☐ | ✅ | ☐ |
| 3 | **Khả năng học hỏi** | Chủ động đọc tài liệu, thử nghiệm dịch vụ mới và điều chỉnh giải pháp khi gặp giới hạn | ✅ | ☐ | ☐ |
| 4 | **Tư duy giải quyết vấn đề** | Phân tích lỗi xác thực, upload tệp lớn, xử lý bất đồng bộ và quyền truy cập tài nguyên | ☐ | ✅ | ☐ |
| 5 | **Bảo mật cloud** | Áp dụng JWT, S3 private, presigned URL, CORS và least privilege | ☐ | ✅ | ☐ |
| 6 | **Quản lý thời gian** | Hoàn thành dự án, workshop và báo cáo theo từng giai đoạn | ☐ | ✅ | ☐ |
| 7 | **Tính chủ động** | Tự nghiên cứu kiến trúc, chi phí, rủi ro và phương án tối ưu | ✅ | ☐ | ☐ |
| 8 | **Giao tiếp và trình bày** | Viết báo cáo song ngữ, tổng hợp sự kiện và trình bày kiến thức theo cấu trúc | ☐ | ✅ | ☐ |
| 9 | **Hợp tác và tiếp nhận phản hồi** | Trao đổi với mentor, tiếp thu góp ý và cập nhật sản phẩm/tài liệu | ☐ | ✅ | ☐ |
| 10 | **Tinh thần trách nhiệm** | Theo dõi tiến độ, kiểm thử và dọn dẹp tài nguyên AWS sau thực hành | ✅ | ☐ | ☐ |
| 11 | **Đóng góp cho chương trình** | Hoàn thiện tài liệu workshop, blog và báo cáo có thể chia sẻ lại cho cộng đồng | ☐ | ✅ | ☐ |
| 12 | **Đánh giá tổng thể** | Hoàn thành mục tiêu chính và có sản phẩm AWS hoạt động, nhưng vẫn còn dư địa nâng cao | ☐ | ✅ | ☐ |

## Hạn chế và nội dung cần cải thiện

- Kiến thức về **observability** vẫn ở mức cơ bản; cần bổ sung CloudWatch Dashboard, Alarm, tracing và quy trình phản ứng sự cố.
- Cần tăng độ bao phủ kiểm thử tự động cho frontend, backend, xác thực và các luồng bất đồng bộ của Amazon Transcribe.
- Cần luyện tập thêm về thiết kế IAM policy chi tiết, quản lý secret và kiểm thử bảo mật ứng dụng.
- Khả năng ước tính chi phí hiện chủ yếu dựa trên giả định; cần theo dõi metric sử dụng thực tế và thiết lập AWS Budgets.
- Cần cải thiện kỹ năng thuyết trình, giao tiếp kỹ thuật bằng tiếng Anh và diễn giải kiến trúc ngắn gọn hơn.
- Cần quản lý thời gian đều đặn hơn, tránh tập trung quá nhiều công việc vào cuối mỗi giai đoạn.

## Định hướng tiếp theo

Trong thời gian tới, tôi muốn hoàn thiện monitoring và automated testing cho Polly Voice, bổ sung lifecycle policy cho S3, cải thiện giao diện và nghiên cứu các tính năng như tạo phụ đề, dịch transcript hoặc lồng tiếng tự động. Tôi cũng sẽ tiếp tục củng cố kiến thức nền tảng để chuẩn bị cho chứng chỉ AWS Certified Cloud Practitioner và các chứng chỉ AWS ở cấp độ cao hơn.
