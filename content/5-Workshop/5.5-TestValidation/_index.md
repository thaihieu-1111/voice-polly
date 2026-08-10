---
title: "Test and Validation"
date: 2026-07-30
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

# Test and Validation

This section validates the deployed Polly Voice system by sending requests, reviewing logs and metrics, testing failure cases, and comparing the observed behavior with expected results.

## 1. Prepare the test environment

Record the Amplify URL, API Gateway invoke URL, Cognito test account, Lambda function name, S3 bucket, and DynamoDB table.

![Test environment](/images/5-Workshop/5.5-TestValidation/01-test-environment.png)

## 2. Send requests

### Health and voices

Send:

```text
GET <API_URL>/health
GET <API_URL>/api/v1/voices
```

The health endpoint should return HTTP `200`, service `polly-voice-backend`, and Region `eu-north-1`.

![Successful health request](/images/5-Workshop/5.5-TestValidation/02-health-request.png)

### Text-to-Speech

Sign in, enter text, select a compatible voice and engine, and run Preview and Create Audio.

![TTS request and response](/images/5-Workshop/5.5-TestValidation/03-tts-request.png)

### Speech-to-Text

Request a presigned upload URL, upload a valid media file directly to S3, create the Transcribe job, wait for `COMPLETED`, and verify the transcript.

![Completed STT request](/images/5-Workshop/5.5-TestValidation/04-stt-result.png)

## 3. Review CloudWatch logs

Open the Lambda function → **Monitor** → **View CloudWatch logs**, select the latest stream, and correlate the request by timestamp or request ID.

![CloudWatch logs](/images/5-Workshop/5.5-TestValidation/05-cloudwatch-logs.png)

## 4. Check metrics

Validate Lambda and API Gateway metrics in CloudWatch.

![CloudWatch metrics](/images/5-Workshop/5.5-TestValidation/06-cloudwatch-metrics.png)

## 5. Test failure cases

Confirm authentication errors and unauthorized requests return HTTP 401.

![Authentication error](/images/5-Workshop/5.5-TestValidation/07-auth-error.png)

## 6. Validation summary

| Item | Result | Evidence |
|---|---|---|
| `/health` endpoint | Pass | HTTP `200` response |
| Voice list | Pass | Supported voices returned |
| Cognito SRP sign-in | Pass | Access token issued |
| Text-to-Speech | Pass | MP3 stored in S3, history saved |
| Speech-to-Text | Pass | Transcribe job completed, text returned |
| CloudWatch logs | Pass | Request logged without errors |
| Alarm configuration | Pass | CloudWatch alarms active |

![Validation summary](/images/5-Workshop/5.5-TestValidation/08-test-summary.png)
