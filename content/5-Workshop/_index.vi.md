---
title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

## Tổng quan

**Xây dựng ứng dụng Polly Voice trên kiến trúc AWS Serverless**

**Polly Voice** là một ứng dụng web xử lý giọng nói được xây dựng hoàn toàn trên nền tảng **Amazon Web Services (AWS)** theo kiến trúc **Serverless**.

Ứng dụng cho phép người dùng đăng ký tài khoản, đăng nhập và sử dụng hai chức năng chính:

* **Text-to-Speech (TTS):** Người dùng nhập nội dung văn bản, lựa chọn giọng đọc và tạo tệp âm thanh MP3 bằng dịch vụ **Amazon Polly**.
* **Speech-to-Text (STT):** Người dùng tải lên tệp âm thanh, hệ thống sử dụng **Amazon Transcribe** để chuyển đổi nội dung giọng nói thành văn bản.

Sau khi quá trình xử lý hoàn tất, các tệp âm thanh được lưu trữ trên **Amazon S3**, trong khi lịch sử chuyển đổi của cả hai chức năng được lưu trong **Amazon DynamoDB**, cho phép người dùng xem lại hoặc tải xuống các kết quả đã tạo bất cứ lúc nào.

Trong workshop này, toàn bộ hệ thống sẽ được triển khai bằng các dịch vụ được quản lý hoàn toàn của AWS (Fully Managed Services), giúp loại bỏ việc quản lý máy chủ, giảm chi phí vận hành và dễ dàng mở rộng khi số lượng người dùng tăng lên.

Thông qua workshop, chúng ta sẽ hiểu cách nhiều dịch vụ AWS phối hợp với nhau để xây dựng một ứng dụng web hoàn chỉnh theo mô hình cloud-native, bao gồm xác thực người dùng bằng **Amazon Cognito**, xử lý nghiệp vụ với **AWS Lambda**, giao tiếp thông qua **Amazon API Gateway**, chuyển đổi văn bản thành giọng nói bằng **Amazon Polly**, chuyển đổi giọng nói thành văn bản bằng **Amazon Transcribe**, lưu trữ dữ liệu trên **Amazon S3** và **Amazon DynamoDB**, đồng thời triển khai giao diện người dùng bằng **AWS Amplify**.

#### Nội dung

1. [Tổng quan về workshop](5.1-Workshop-overview/)
2. [Chuẩn bị](5.2-Prerequiste/)
3. [Cấu hình và triển khai Backend](5.3-DeployBackend/)
4. [Triển khai Frontend](5.4-DeployFrontend/)
5. [Thiết lập quy trình CI/CD](5.5-ContinuousIntegration/)
6. [Kiểm thử và xác thực hệ thống](5.5-TestValidation/)
7. [Dọn dẹp tài nguyên](5.6-Cleanup/)

Sau khi hoàn thành workshop, chúng ta sẽ triển khai thành công một ứng dụng **AI Voice** hoàn chỉnh, hỗ trợ cả **Text-to-Speech** và **Speech-to-Text**, tuân theo kiến trúc **AWS Serverless** hiện đại và các nguyên tắc bảo mật theo AWS Best Practices.
