---
title: "Triển khai Backend bằng AWS SAM"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

AWS Serverless Application Model (AWS SAM) được sử dụng để mô tả, build và triển
khai toàn bộ backend Polly Voice. SAM chuyển `template.yaml` thành CloudFormation
stack, nhờ đó các tài nguyên có thể được tạo và cập nhật nhất quán.



1. Kiểm tra AWS CLI đang đăng nhập đúng tài khoản và đúng region:

```powershell
aws sts get-caller-identity
aws configure get region
```

Account ID phải là tài khoản dùng cho workshop và region phải là:

```text
eu-north-1
```

![Verify AWS identity](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/01-aws-identity.png)

{{% notice warning %}}
Không tiếp tục deploy nếu `aws sts get-caller-identity` trả về tài khoản cũ hoặc
không đúng tài khoản thực hành.
{{% /notice %}}

2. Cài dependencies và kiểm tra source:

```powershell
cd backend
npm ci
npm run typecheck
npm test
```

`npm ci` sử dụng `package-lock.json` để tạo dependency tree có thể lặp lại.
Typecheck và test được thực hiện trước khi đóng gói Lambda.

![Backend checks succeeded](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/03-backend-checks.png)

3. Kiểm tra SAM template:

```powershell
sam validate
```

`template.yaml` khai báo:

- Lambda runtime Node.js 22, kiến trúc arm64.
- API Gateway HTTP API.
- DynamoDB table sử dụng on-demand billing.
- Private S3 media bucket.
- Lambda environment variables.
- IAM policy cho Polly, Transcribe, S3 và DynamoDB.
- CloudWatch Logs và X-Ray tracing.

![SAM validation succeeded](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/04-sam-validate.png)

4. Build Lambda package:

```powershell
sam build --no-cached
```

SAM sử dụng esbuild với entry point:

```text
src/lambda.ts
```

Build thành công tạo artifact trong:

```text
backend/.aws-sam/build/
```

![SAM build succeeded](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/05-sam-build.png)

5. Chuẩn bị các parameter cần thiết:

| Parameter | Giá trị |
|---|---|
| Stack name | `polly-voice-api` |
| Region | `eu-north-1` |
| MediaBucketName | `polly-voice-media-<ACCOUNT_ID>-eu-north-1` |
| HistoryTableName | `polly-voice-history` |
| CognitoUserPoolId | User Pool ID của Cognito |
| CognitoClientId | SPA App Client ID |
| AllowedOrigins | `http://localhost:5173` trong lần deploy đầu |
| TranscribeLanguageCode | `en-US` |

Tên S3 bucket phải duy nhất trên toàn cầu. Việc thêm Account ID giúp giảm khả
năng trùng tên.

![Backend deployment parameters](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/1.png)

6. Cấu hình trực tiếp tham số triển khai vào file `samconfig.toml`:

Tạo hoặc cập nhật file `samconfig.toml` tại thư mục `backend` với các thông số đã chuẩn bị:

```toml
version = 0.1

[default.deploy.parameters]
stack_name = "polly-voice-api"
capabilities = "CAPABILITY_IAM"
s3_bucket = "polly-voice-sam-artifacts-<ACCOUNT_ID>-eu-north-1"
s3_prefix = "polly-voice-api"
confirm_changeset = true
disable_rollback = false
parameter_overrides = "MediaBucketName=\"polly-voice-media-<ACCOUNT_ID>-eu-north-1\" HistoryTableName=\"polly-voice-history\" CognitoUserPoolId=\"<COGNITO_USER_POOL_ID>\" CognitoClientId=\"<COGNITO_CLIENT_ID>\" AllowedOrigins=\"http://localhost:5173\" TranscribeLanguageCode=\"en-US\""

[default.global.parameters]
region = "eu-north-1"
```

![Cấu hình samconfig.toml](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/1.png)

7. Thực hiện triển khai Backend:

```powershell
sam deploy
```

`CAPABILITY_IAM` xác nhận rằng CloudFormation được phép tạo Lambda execution role từ policy trong SAM template. Việc đưa sẵn cấu hình vào file `samconfig.toml` giúp câu lệnh `sam deploy` chạy tự động 100%, nhanh chóng và tránh các lỗi gõ sai thông số khi tương tác thủ công.

Các resource chính:

```text
AWS::Serverless::HttpApi
AWS::Serverless::Function
AWS::S3::Bucket
AWS::DynamoDB::Table
AWS::IAM::Role
```

Chờ CloudFormation hoàn tất. Stack phải có trạng thái:

```text
CREATE_COMPLETE
```

SAM hiển thị Outputs:

```text
ApiUrl
FunctionName
MediaBucket
HistoryTable
```
![CloudFormation change set](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/08-change-set.png)
![CloudFormation change set](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/2.png)
9. Ghi lại `ApiUrl` trong:

```text
AWS CloudFormation
→ Stacks
→ polly-voice-api
→ Outputs
```

URL có dạng:

```text
https://<API_ID>.execute-api.eu-north-1.amazonaws.com
```

Frontend sử dụng:

```text
https://<API_ID>.execute-api.eu-north-1.amazonaws.com/api/v1
```

![CloudFormation outputs](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/11-cloudformation-outputs.png)

## Quyền để Lambda gọi các dịch vụ AWS

SAM tạo execution role cho Lambda từ phần `Policies`:

```yaml
Policies:
  - DynamoDBCrudPolicy:
      TableName: !Ref HistoryTable
  - S3CrudPolicy:
      BucketName: !Ref MediaBucket
  - Statement:
      - Effect: Allow
        Action:
          - polly:SynthesizeSpeech
          - polly:DescribeVoices
          - transcribe:StartTranscriptionJob
          - transcribe:GetTranscriptionJob
          - transcribe:DeleteTranscriptionJob
        Resource: "*"
```

AWS SDK trong Lambda tự nhận temporary credentials của execution role. Project
không lưu access key hoặc secret access key trong source và environment
variables.

## Kết quả

Backend đã được triển khai dưới dạng CloudFormation stack. API Gateway HTTP API,
Lambda, private S3 bucket và DynamoDB table được tạo tại `eu-north-1`. Outputs
của stack cung cấp các giá trị cần thiết để kiểm tra backend và kết nối
frontend.

