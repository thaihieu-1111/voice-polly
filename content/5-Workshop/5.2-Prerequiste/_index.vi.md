+++
title = '5.2 Chuẩn bị'
weight = 2

[params]
collapsibleMenu = true
+++

## Chuẩn bị

Trước khi bắt đầu workshop, chúng ta cần chuẩn bị môi trường AWS và một số công cụ phát triển cần thiết. Những yêu cầu dưới đây sẽ giúp đảm bảo quá trình triển khai và kiểm thử ứng dụng diễn ra thuận lợi.

---

## Tài khoản AWS

Chúng ta cần một tài khoản AWS đang hoạt động và có đầy đủ quyền để tạo và quản lý các tài nguyên trên nền tảng AWS.

Workshop sẽ sử dụng các dịch vụ sau:

* AWS Amplify
* Amazon Cognito
* Amazon API Gateway
* AWS Lambda
* Amazon Polly
* Amazon Transcribe
* Amazon S3
* Amazon DynamoDB
* AWS Identity and Access Management (IAM)
* Amazon CloudWatch

---

## AWS Region

Trong suốt workshop, chúng ta sẽ sử dụng Region sau:

```text
ap-southeast-1 (Singapore)
```

Việc sử dụng cùng một Region cho tất cả các dịch vụ sẽ giúp đơn giản hóa quá trình triển khai và hạn chế các vấn đề về khả năng tương thích giữa các dịch vụ.

---

## Quyền IAM cần thiết

Tài khoản AWS cần có quyền tạo và quản lý các tài nguyên sau:

* IAM Roles
* Amazon Cognito User Pools
* AWS Lambda Functions
* Amazon API Gateway
* Amazon S3 Buckets
* Amazon DynamoDB Tables
* AWS Amplify Applications
* Amazon Polly
* Amazon Transcribe
* Amazon CloudWatch Logs

Để tăng cường bảo mật, chúng ta nên tuân theo nguyên tắc **Least Privilege**, chỉ cấp những quyền cần thiết để hoàn thành workshop.

---

## Môi trường phát triển

Trước khi triển khai ứng dụng, chúng ta cần cài đặt các phần mềm sau.

### Node.js

Cài đặt **Node.js** (phiên bản LTS) cùng với **npm**.

Frontend của ứng dụng được phát triển bằng **React** và **Vite**.

---

### Visual Studio Code

Khuyến nghị sử dụng **Visual Studio Code** làm trình soạn thảo mã nguồn.

Một số tiện ích mở rộng hữu ích gồm:

* ESLint
* Prettier
* AWS Toolkit

---

### Git

Git được sử dụng để quản lý phiên bản và mã nguồn của dự án.

Ngoài ra, chúng ta cũng nên tạo một repository trên GitHub để sao lưu mã nguồn và phục vụ cho việc triển khai sau này.

---

## Cấu trúc dự án

Dự án được chia thành hai thành phần chính:

```text
Polly Voice
│
├── frontend
│   ├── React
│   ├── TypeScript
│   └── Vite
│
└── backend
    ├── AWS Lambda
    ├── Amazon Polly
    ├── Amazon Transcribe
    ├── Amazon S3
    ├── Amazon DynamoDB
    └── API Gateway
```

Việc tách riêng frontend và backend giúp ứng dụng dễ phát triển, triển khai và bảo trì hơn.

---

## Tổng quan kiến trúc

Sau khi hoàn thành, ứng dụng sẽ hoạt động theo quy trình sau:

```text
User
   │
   ▼
AWS Amplify
   │
   ▼
Amazon Cognito
   │
   ▼
API Gateway
   │
   ▼
AWS Lambda
   │
   ├────────► Amazon Polly
   │              │
   │              ▼
   │         Speech Audio
   │
   ├────────► Amazon Transcribe
   │              │
   │              ▼
   │        Transcribed Text
   │
   ├────────► Amazon S3
   │
   └────────► Amazon DynamoDB
```

Người dùng sẽ được xác thực thông qua **Amazon Cognito** trước khi gửi yêu cầu đến **API Gateway**. Sau đó, **AWS Lambda** sẽ xử lý yêu cầu:

* Gọi **Amazon Polly** để chuyển đổi văn bản thành giọng nói (Text-to-Speech).
* Gọi **Amazon Transcribe** để chuyển đổi giọng nói thành văn bản (Speech-to-Text).
* Lưu các tệp âm thanh trên **Amazon S3**.
* Ghi thông tin lịch sử vào **Amazon DynamoDB**.
* Trả kết quả về cho ứng dụng frontend.

---

## Trước khi bắt đầu

Trước khi chuyển sang phần tiếp theo, hãy đảm bảo rằng:

* Chúng ta đã có tài khoản AWS đang hoạt động.
* Tất cả các dịch vụ AWS cần thiết đều khả dụng trong Region đã chọn.
* Node.js và Visual Studio Code đã được cài đặt.
* Mã nguồn của dự án đã được chuẩn bị.
* Chúng ta đã hiểu tổng quan về kiến trúc của ứng dụng.

Sau khi hoàn thành các bước chuẩn bị trên, chúng ta sẽ bắt đầu thiết kế kiến trúc giải pháp ở phần tiếp theo.
