---
title: "Kiểm tra Backend sau khi triển khai"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

Phần này xác nhận CloudFormation đã tạo đúng tài nguyên và request có thể đi từ
API Gateway đến Lambda, Amazon Polly, Amazon S3 và Amazon DynamoDB.

1. Mở CloudFormation stack `polly-voice-api` và kiểm tra trạng thái
`CREATE_COMPLETE`.

![CloudFormation CREATE_COMPLETE](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/01-stack-complete.png)

2. Trong tab **Resources**, kiểm tra các tài nguyên chính:

- API Gateway HTTP API.
- Lambda function.
- Lambda execution role.
- S3 media bucket.
- DynamoDB history table.

![CloudFormation resources](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/02-stack-resources.png)


3. Mở **API Gateway**, kiểm tra API được tạo là **HTTP API** và integration trỏ
tới Lambda của stack.

SAM sử dụng proxy route để chuyển request tới Express:

```text
ANY /
ANY /{proxy+}
```

![API Gateway routes](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/04-api-gateway-routes.png)

4. Mở Lambda function và kiểm tra:

| Cấu hình | Giá trị |
|---|---|
| Runtime | Node.js 22.x |
| Architecture | arm64 |
| Memory | 1024 MB |
| Timeout | 60 seconds |
| Handler | `lambda.handler` |
| Tracing | Active |

![Lambda configuration](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/05-lambda-configuration.png)

5. Trong **Configuration → Environment variables**, kiểm tra tên các biến:

```text
AWS_ENABLED
AWS_DYNAMODB_TABLE_NAME
AWS_S3_MEDIA_BUCKET
AWS_COGNITO_USER_POOL_ID
AWS_COGNITO_CLIENT_ID
AWS_COGNITO_ISSUER_URI
AWS_TRANSCRIBE_LANGUAGE_CODE
ALLOWED_ORIGINS
```

Không đưa credential vào Lambda environment variables.

![Lambda environment variables](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/06-lambda-environment.png)


7. Mở S3 bucket được tạo từ CloudFormation Outputs. Kiểm tra:

- Block Public Access được bật.
- Default encryption được bật.
- Bucket không có public bucket policy.
- CORS cho phép origin frontend dự kiến.

![Private S3 bucket](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/08-s3-security.png)
