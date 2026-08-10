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

3. Trong tab **Outputs**, sao chép `ApiUrl` và mở health endpoint:

```text
https://<API_ID>.execute-api.eu-north-1.amazonaws.com/health
```

Kết quả mong đợi:

```json
{
  "status": "ok",
  "service": "polly-voice-backend",
  "region": "eu-north-1"
}
```

![Health endpoint response](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/03-health-response.png)

4. Mở **API Gateway**, kiểm tra API được tạo là **HTTP API** và integration trỏ
tới Lambda của stack.

SAM sử dụng proxy route để chuyển request tới Express:

```text
ANY /
ANY /{proxy+}
```

![API Gateway routes](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/04-api-gateway-routes.png)

5. Mở Lambda function và kiểm tra:

| Cấu hình | Giá trị |
|---|---|
| Runtime | Node.js 22.x |
| Architecture | arm64 |
| Memory | 1024 MB |
| Timeout | 60 seconds |
| Handler | `lambda.handler` |
| Tracing | Active |

![Lambda configuration](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/05-lambda-configuration.png)

6. Trong **Configuration → Environment variables**, kiểm tra tên các biến:

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

7. Mở Lambda execution role và kiểm tra role có quyền:

- Ghi CloudWatch Logs.
- Gửi trace tới X-Ray.
- Đọc/ghi S3 bucket của project.
- Đọc/ghi DynamoDB table của project.
- `polly:SynthesizeSpeech`.
- Tạo và đọc trạng thái Transcribe job.

Role không được có `AdministratorAccess`.

![Lambda execution role](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/07-lambda-role.png)

8. Mở S3 bucket được tạo từ CloudFormation Outputs. Kiểm tra:

- Block Public Access được bật.
- Default encryption được bật.
- Bucket không có public bucket policy.
- CORS cho phép origin frontend dự kiến.

![Private S3 bucket](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/08-s3-security.png)

9. Mở DynamoDB table `polly-voice-history`. Kiểm tra:

- Billing mode là on-demand.
- Partition key là `PK`.
- Sort key là `SK`.
- Global Secondary Index là `EntityIndex`.
- Encryption at rest được bật.

![DynamoDB table](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/09-dynamodb-table.png)

10. Gửi một TTS Preview request từ frontend hoặc API client. Luồng xử lý:

```text
API Gateway
→ Lambda
→ SynthesizeSpeechCommand
→ Amazon Polly
→ AudioStream
→ S3
→ Presigned download URL
```

Kết quả mong đợi là HTTP 201 và response chứa thông tin media.

![TTS API response](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/10-tts-response.png)

11. Kiểm tra S3 có file audio trong prefix `preview/` hoặc `tts/`.

![TTS object in S3](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/11-s3-audio-object.png)

12. Mở Lambda **Monitor → View CloudWatch logs** và kiểm tra log request:

```text
Lambda request received
Lambda request completed
```

Nếu API trả HTTP 500, tìm error đầu tiên trong log stream thay vì chỉ dựa vào
thông báo `Internal Server Error` của API Gateway.

![CloudWatch Lambda logs](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/12-cloudwatch-logs.png)

## Các lỗi thường gặp

| Lỗi | Nguyên nhân | Cách xử lý |
|---|---|---|
| API trả HTTP 500 | Lambda handler, dependency hoặc environment variable lỗi | Xem CloudWatch log stream mới nhất |
| `AccessDeniedException` | Execution role thiếu action | Cập nhật policy trong `template.yaml` và deploy lại |
| `ResourceNotFoundException` | Sai bucket/table/region | Kiểm tra CloudFormation Outputs và Lambda variables |
| `Invalid JWT` hoặc HTTP 401 | Cognito issuer, pool hoặc client không đồng bộ | Dùng cùng Cognito IDs cho frontend và backend |
| CORS error | `ALLOWED_ORIGINS` chưa có Amplify domain | Cập nhật SAM parameter và deploy lại |
| Polly báo engine/voice lỗi | Voice không hỗ trợ engine tại region | Dùng tổ hợp voice–engine hợp lệ |
| Transcribe thất bại | Sai format hoặc language code | Kiểm tra job FailureReason |

## Kết quả

Backend đã vượt qua kiểm tra cơ bản: health endpoint trả HTTP 200, API Gateway
gọi được Lambda, Lambda có execution role đúng phạm vi, S3 và DynamoDB không
public, đồng thời TTS có thể tạo audio thực tế thông qua Amazon Polly.
