---
title: "Test and Validation"
date: 2026-07-30
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

# Test and Validation

This section validates the deployed Polly Voice system by sending requests,
reviewing logs and metrics, testing failure cases, and comparing the observed
behavior with the expected results.

## 1. Prepare the test environment

Record the Amplify URL, API Gateway invoke URL, Cognito test account, Lambda
function name, S3 bucket, and DynamoDB table. Do not expose complete access
tokens, credentials, passwords, or active presigned URLs in screenshots.

![Test environment](/images/5-Workshop/5.5-TestValidation/01-test-environment.png)

## 2. Send requests

### Health and voices

Send:

```text
GET <API_URL>/health
GET <API_URL>/api/v1/voices
```

The health endpoint should return HTTP `200`, service
`polly-voice-backend`, and Region `eu-north-1`. The voices endpoint should
return the supported voice list.

![Successful health request](/images/5-Workshop/5.5-TestValidation/02-health-request.png)

### Text-to-Speech

Sign in, enter text, select a compatible voice and engine, and run Preview and
Create Audio. Inspect the Network request and confirm that the Cognito bearer
token is included when required.

Expected results:

- The preview audio plays.
- The completed audio can be downloaded.
- The audio object exists in S3.
- The history item exists in DynamoDB and appears in the frontend.

![TTS request and response](/images/5-Workshop/5.5-TestValidation/03-tts-request.png)

### Speech-to-Text

Request a presigned upload URL, upload a valid media file directly to S3, create
the Transcribe job, wait for `COMPLETED`, and verify the transcript.

![Completed STT request](/images/5-Workshop/5.5-TestValidation/04-stt-result.png)

## 3. Review CloudWatch logs

Open the Lambda function → **Monitor** → **View CloudWatch logs**, select the
latest stream, and correlate the request by timestamp or request ID. Verify
`START`, `END`, `REPORT`, and application logs. There should be no
`Runtime.Unknown`, `AccessDeniedException`, module error, or timeout in a
successful request.

Enable and inspect API Gateway access logs for the `$default` stage when needed.
Do not log tokens, sensitive text, or complete presigned URLs.

![CloudWatch logs](/images/5-Workshop/5.5-TestValidation/05-cloudwatch-logs.png)

## 4. Check metrics

Validate:

- Lambda `Invocations`, `Errors`, `Duration`, `Throttles`, and concurrency.
- API Gateway `Count`, `2XX`, `4XX`, `5XX`, `Latency`, and
  `IntegrationLatency`.
- The expected S3 object and DynamoDB item.
- A `COMPLETED` Transcribe job.
- No unexpected alarm in the `ALARM` state.

![CloudWatch metrics](/images/5-Workshop/5.5-TestValidation/06-cloudwatch-metrics.png)

## 5. Test failure cases

| Scenario | Expected result |
|---|---|
| Missing token | HTTP `401`, no protected data |
| Invalid or expired token | HTTP `401` or `403` |
| Disallowed CORS origin | Browser blocks the request |
| Empty or invalid body | HTTP `400` with a useful message |
| Unsupported Polly voice/engine | HTTP `400`, no false completed record |
| Unsupported or oversized media | HTTP `400`/`413` |
| Missing IAM permission in a test environment | `5xx` and an `AccessDenied` log |
| Unknown entity ID | HTTP `404` |
| Unknown API route | HTTP `404` |

Restore any intentionally modified test permission immediately afterward.

![Authentication failure test](/images/5-Workshop/5.5-TestValidation/07-auth-error.png)

## 6. Expected outcome

The validation passes when authentication works, the health endpoint returns
HTTP `200`, TTS audio can be played and downloaded, STT produces a transcript,
history is isolated by user, S3 remains private, DynamoDB stores the expected
items, valid flows do not produce `5XX`, and invalid requests return controlled
`4XX` responses with traceable CloudWatch logs.

## 7. Test record

| Test ID | Test | Expected | Actual | Status |
|---|---|---|---|---|
| TC-01 | Health API | HTTP 200 | Complete after test | Pass/Fail |
| TC-02 | Cognito sign-in | Valid session | Complete after test | Pass/Fail |
| TC-03 | TTS Preview | Audio plays | Complete after test | Pass/Fail |
| TC-04 | Create/download MP3 | Download succeeds | Complete after test | Pass/Fail |
| TC-05 | STT | Transcript returned | Complete after test | Pass/Fail |
| TC-06 | History | Correct user data | Complete after test | Pass/Fail |
| TC-07 | Missing token | HTTP 401 | Complete after test | Pass/Fail |
| TC-08 | Invalid input | HTTP 400 | Complete after test | Pass/Fail |
| TC-09 | Unknown route | HTTP 404 | Complete after test | Pass/Fail |
| TC-10 | CloudWatch | Logs and metrics available | Complete after test | Pass/Fail |

![Test summary](/images/5-Workshop/5.5-TestValidation/08-test-summary.png)

