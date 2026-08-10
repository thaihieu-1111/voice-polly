---
title: "Triển khai Backend"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

Trong phần này, các dịch vụ AWS cho backend Node.js/Express của Polly Voice được
cấu hình, kiểm tra, sau đó đóng gói và triển khai theo kiến trúc serverless bằng
AWS SAM.

Nội dung gồm:

1. Cấu hình các dịch vụ AWS phục vụ quá trình phát triển backend bằng Console.
2. Triển khai backend bằng AWS SAM và CloudFormation.
3. Kiểm tra API Gateway, Lambda và các tài nguyên backend sau triển khai.

Phần cấu hình trước triển khai ưu tiên AWS Management Console để trình bày rõ vai
trò của IAM, S3, DynamoDB, Polly, Transcribe, Lambda, API Gateway, Cognito và
CloudWatch. Mã nguồn chỉ đảm nhiệm logic nghiệp vụ và gọi dịch vụ qua AWS SDK;
thông tin môi trường được cấu hình bên ngoài mã nguồn.

Backend sau khi triển khai sử dụng:

- Amazon API Gateway HTTP API làm public endpoint.
- AWS Lambda chạy Node.js/Express.
- Amazon Polly tổng hợp giọng nói.
- Amazon Transcribe chuyển media thành văn bản.
- Amazon S3 lưu media và transcript.
- Amazon DynamoDB lưu lịch sử xử lý.
- Amazon CloudWatch và AWS X-Ray phục vụ giám sát.
