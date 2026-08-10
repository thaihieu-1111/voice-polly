---
title: "Verify the Deployed Backend"
date: 2026-07-30
weight: 3
chapter: false
pre: " <b> 5.4.3. </b> "
---

This section verifies that CloudFormation created the correct resources and that
requests can flow from API Gateway to Lambda, Amazon Polly, Amazon S3, and
Amazon DynamoDB.

1. Open the `polly-voice-api` CloudFormation stack and verify the
`CREATE_COMPLETE` status.

![CloudFormation CREATE_COMPLETE](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/01-stack-complete.png)

2. In the **Resources** tab, verify the main resources:

- API Gateway HTTP API.
- Lambda function.
- Lambda execution role.
- S3 media bucket.
- DynamoDB history table.

![CloudFormation resources](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/02-stack-resources.png)

3. In the **Outputs** tab, copy `ApiUrl` and open the health endpoint:

```text
https://<API_ID>.execute-api.eu-north-1.amazonaws.com/health
```

Expected response:

```json
{
  "status": "ok",
  "service": "polly-voice-backend",
  "region": "eu-north-1"
}
```

![Health endpoint response](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/03-health-response.png)

4. Open **API Gateway**, verify that the created API is an **HTTP API**, and
confirm that its integration targets the stack Lambda function.

SAM uses proxy routes to forward requests to Express:

```text
ANY /
ANY /{proxy+}
```

![API Gateway routes](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/04-api-gateway-routes.png)

5. Open the Lambda function and verify:

| Setting | Value |
|---|---|
| Runtime | Node.js 22.x |
| Architecture | arm64 |
| Memory | 1024 MB |
| Timeout | 60 seconds |
| Handler | `lambda.handler` |
| Tracing | Active |

![Lambda configuration](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/05-lambda-configuration.png)

6. Under **Configuration → Environment variables**, verify these variable names:

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

Do not place credentials in Lambda environment variables.

![Lambda environment variables](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/06-lambda-environment.png)

7. Open the Lambda execution role and verify permissions for:

- Writing CloudWatch Logs.
- Sending traces to X-Ray.
- Reading and writing the project S3 bucket.
- Reading and writing the project DynamoDB table.
- `polly:SynthesizeSpeech`.
- Starting and checking Transcribe jobs.

The role must not have `AdministratorAccess`.

![Lambda execution role](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/07-lambda-role.png)

8. Open the S3 bucket from the CloudFormation Outputs. Verify:

- Block Public Access is enabled.
- Default encryption is enabled.
- The bucket has no public bucket policy.
- CORS allows the intended frontend origin.

![Private S3 bucket](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/08-s3-security.png)

9. Open the `polly-voice-history` DynamoDB table. Verify:

- The billing mode is on-demand.
- The partition key is `PK`.
- The sort key is `SK`.
- The Global Secondary Index is `EntityIndex`.
- Encryption at rest is enabled.

![DynamoDB table](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/09-dynamodb-table.png)

10. Send a TTS Preview request from the frontend or an API client. Processing
flow:

```text
API Gateway
→ Lambda
→ SynthesizeSpeechCommand
→ Amazon Polly
→ AudioStream
→ S3
→ Presigned download URL
```

The expected result is HTTP 201 with media information in the response.

![TTS API response](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/10-tts-response.png)

11. Verify that S3 contains an audio object under the `preview/` or `tts/`
prefix.

![TTS object in S3](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/11-s3-audio-object.png)

12. Open Lambda **Monitor → View CloudWatch logs** and verify the request logs:

```text
Lambda request received
Lambda request completed
```

If the API returns HTTP 500, inspect the first error in the latest log stream
instead of relying only on the API Gateway `Internal Server Error` response.

![CloudWatch Lambda logs](/images/5-Workshop/5.3-DeployBackend/5.3.3-verify/12-cloudwatch-logs.png)

## Common issues

| Error | Cause | Resolution |
|---|---|---|
| API returns HTTP 500 | Lambda handler, dependency, or environment variable failure | Inspect the latest CloudWatch log stream |
| `AccessDeniedException` | The execution role is missing an action | Update the policy in `template.yaml` and redeploy |
| `ResourceNotFoundException` | Incorrect bucket, table, or region | Verify CloudFormation Outputs and Lambda variables |
| `Invalid JWT` or HTTP 401 | Cognito issuer, pool, or client mismatch | Use the same Cognito IDs in the frontend and backend |
| CORS error | `ALLOWED_ORIGINS` does not include the Amplify domain | Update the SAM parameter and redeploy |
| Polly engine/voice error | The voice does not support the engine in the region | Use a valid voice–engine combination |
| Transcribe fails | Invalid format or language code | Inspect the job FailureReason |

## Result

The backend has passed its initial verification: the health endpoint returns
HTTP 200, API Gateway invokes Lambda, Lambda has a scoped execution role, S3 and
DynamoDB are not public, and TTS can create real audio through Amazon Polly.
