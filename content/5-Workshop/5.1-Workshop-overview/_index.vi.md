+++
title = '5.1 Tổng quan'
weight = 1

[params]
collapsibleMenu = true
+++

## Tổng quan

Trong workshop này, chúng ta sẽ xây dựng **Polly Voice**, một ứng dụng web xử lý giọng nói theo kiến trúc cloud-native, sử dụng các công nghệ Serverless trên nền tảng Amazon Web Services (AWS).

Ứng dụng cung cấp hai chức năng chính:

* **Text-to-Speech (TTS):** Chuyển đổi văn bản thành giọng nói tự nhiên bằng **Amazon Polly**.
* **Speech-to-Text (STT):** Chuyển đổi giọng nói thành văn bản bằng **Amazon Transcribe**.

Người dùng có thể đăng nhập an toàn, tạo tệp âm thanh từ văn bản, tải lên tệp âm thanh để nhận dạng nội dung, lưu trữ các tệp âm thanh trên **Amazon S3** và quản lý lịch sử chuyển đổi thông qua **Amazon DynamoDB**.

Thay vì triển khai và vận hành các máy chủ truyền thống, toàn bộ ứng dụng được xây dựng bằng các dịch vụ được quản lý hoàn toàn của AWS, giúp tự động mở rộng, giảm chi phí vận hành và đơn giản hóa việc quản lý hạ tầng.

Workshop này minh họa cách nhiều dịch vụ AWS có thể được tích hợp để xây dựng một ứng dụng cloud hoàn chỉnh theo kiến trúc serverless và tuân thủ các phương pháp bảo mật tốt nhất của AWS.

---

## Mục tiêu học tập

Sau khi hoàn thành workshop này, chúng ta sẽ có thể:

* Hiểu cách thiết kế một ứng dụng Serverless trên AWS.
* Cấu hình Amazon Cognito để xác thực người dùng.
* Xây dựng REST API bằng Amazon API Gateway và AWS Lambda.
* Chuyển đổi văn bản thành giọng nói bằng Amazon Polly.
* Chuyển đổi giọng nói thành văn bản bằng Amazon Transcribe.
* Lưu trữ tệp âm thanh trên Amazon S3.
* Lưu trữ dữ liệu ứng dụng trong Amazon DynamoDB.
* Triển khai ứng dụng React bằng AWS Amplify.
* Bảo vệ API bằng cơ chế xác thực JWT.
* Áp dụng nguyên tắc phân quyền tối thiểu (IAM Least Privilege).
* Kiểm thử và xác thực toàn bộ quy trình hoạt động của ứng dụng.

---

## Kiến trúc Workshop

Ứng dụng Polly Voice sử dụng các dịch vụ AWS sau:

* AWS Amplify
* Amazon Cognito
* Amazon API Gateway
* AWS Lambda
* Amazon Polly
* Amazon Transcribe
* Amazon S3
* Amazon DynamoDB

Mỗi dịch vụ đảm nhiệm một chức năng riêng trong hệ thống và phối hợp với nhau để tạo thành một kiến trúc Serverless hoàn chỉnh. Trong đó, **Amazon Polly** chịu trách nhiệm chuyển đổi văn bản thành giọng nói (TTS), còn **Amazon Transcribe** thực hiện chuyển đổi giọng nói thành văn bản (STT).
