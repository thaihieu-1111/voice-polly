---
title: "Deploy the Backend with AWS SAM"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.4.2. </b> "
---

The AWS Serverless Application Model (AWS SAM) is used to define, build, and deploy the entire Polly Voice backend. SAM transforms `template.yaml` into a CloudFormation stack, allowing resources to be created and updated consistently.

1. Verify AWS CLI identity and region:

```powershell
aws sts get-caller-identity
aws configure get region
```

Account ID must match the workshop account and region must be:

```text
eu-north-1
```

![Verify AWS identity](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/01-aws-identity.png)

{{% notice warning %}}
Do not proceed if `aws sts get-caller-identity` returns an incorrect account.
{{% /notice %}}

2. Install dependencies and run backend checks:

```powershell
cd backend
npm ci
npm run typecheck
npm test
```

![Backend checks succeeded](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/03-backend-checks.png)

3. Validate the SAM template:

```powershell
sam validate
```

![SAM validation succeeded](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/04-sam-validate.png)

4. Build the Lambda package:

```powershell
sam build --no-cached
```

![SAM build succeeded](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/05-sam-build.png)

5. Prepare deployment parameters:

| Parameter | Value |
|---|---|
| Stack name | `polly-voice-api` |
| Region | `eu-north-1` |
| MediaBucketName | `polly-voice-media-<ACCOUNT_ID>-eu-north-1` |
| HistoryTableName | `polly-voice-history` |
| CognitoUserPoolId | Cognito User Pool ID |
| CognitoClientId | SPA App Client ID |
| AllowedOrigins | `http://localhost:5173` |
| TranscribeLanguageCode | `en-US` |

![Backend deployment parameters](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/1.png)

6. Configure deployment parameters directly in `samconfig.toml`:

Create or update `samconfig.toml` in the `backend` directory with prepared parameters:

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

![samconfig.toml configuration](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/1.png)

7. Deploy the Backend:

```powershell
sam deploy
```

Main resources:

```text
AWS::Serverless::HttpApi
AWS::Serverless::Function
AWS::S3::Bucket
AWS::DynamoDB::Table
AWS::IAM::Role
```

![CloudFormation change set](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/08-change-set.png)
![CloudFormation change set](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/2.png)

9. Record `ApiUrl` from CloudFormation Outputs:

```text
https://<API_ID>.execute-api.eu-north-1.amazonaws.com
```

Frontend API Base URL:

```text
https://<API_ID>.execute-api.eu-north-1.amazonaws.com/api/v1
```

![CloudFormation outputs](/images/5-Workshop/5.3-DeployBackend/5.3.2-sam/11-cloudformation-outputs.png)

## Result

The backend is deployed as a CloudFormation stack. API Gateway HTTP API, Lambda, private S3 bucket, and DynamoDB table are created in `eu-north-1`. Stack Outputs provide connection URLs for the frontend.
