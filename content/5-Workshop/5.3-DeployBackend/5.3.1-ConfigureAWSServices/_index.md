---
title: "Configure AWS Services for the Backend"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

# Configure AWS Services for the Backend

This section prepares the AWS services used by the Polly Voice backend before
the application source code is deployed. The resources are configured in
**Europe (Stockholm) – `eu-north-1`**.

{{% notice info %}}
AWS SAM in the next section can create API Gateway, Lambda, S3, and DynamoDB
automatically. The Console steps below explain how the services are connected.
For a production deployment, use only one source of truth to avoid duplicate
resources: either temporary Console resources or the SAM/CloudFormation stack.
{{% /notice %}}

## 1. Create the Lambda function

1. Open [AWS Lambda](https://eu-north-1.console.aws.amazon.com/lambda/home?region=eu-north-1#/functions) and choose **Create function**.
2. Select **Author from scratch** and enter:

| Setting | Value |
|---|---|
| Function name | `polly-voice` |
| Runtime | Node.js 22.x |
| Architecture | `arm64` |

3. Choose **Create function**.
4. Under **Configuration → General configuration**, set memory to `1024 MB` and
   timeout to `60 seconds`.

![Create Lambda function](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/createfunction.png)

![Lambda author from scratch](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.2.png)

## 2. Create the S3 media bucket

1. Open [Amazon S3](https://eu-north-1.console.aws.amazon.com/s3/home?region=eu-north-1) and choose **Create bucket**.
2. Use a globally unique name:

```text
polly-voice-media-<account-id>-eu-north-1
```

3. Keep **Block all public access** enabled.
4. Enable **Bucket Versioning** when file recovery is required.
5. Select **Server-side encryption with Amazon S3 managed keys (SSE-S3)**.
6. Choose **Create bucket**.

The bucket stores TTS audio, STT input media, and transcription results. It must
remain private; the backend returns presigned URLs when the frontend needs to
upload or download an object.

![Create S3 bucket](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.3.png)

![S3 bucket configuration](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.4.png)

![Bucket versioning](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.5.png)

![S3 bucket encryption](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.6.png)

## 3. Configure an S3 trigger for Lambda

1. Open the `polly-voice` Lambda function and choose **Add trigger**.
2. Select **S3**.
3. Choose the media bucket and select **All object create events**.
4. Acknowledge the recursive invocation warning and choose **Add**.

{{% notice warning %}}
Do not use an unrestricted S3 trigger when the same Lambda writes its output
back to the bucket. That design can create an invocation loop. For the current
API-driven architecture, the trigger is optional; prefer API Gateway for TTS and
use a dedicated worker or a restricted prefix for asynchronous STT processing.
{{% /notice %}}

![Add S3 trigger](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.10.png)

![S3 trigger configuration](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.12.png)

## 4. Create an IAM policy for Lambda

1. In Lambda, choose **Configuration → Permissions**.
2. Open the execution role attached to `polly-voice`.
3. Choose **Add permissions → Create inline policy**.
4. Select S3 and grant the object operations required by the application:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject",
        "s3:DeleteObject"
      ],
      "Resource": "arn:aws:s3:::polly-voice-media-<account-id>-eu-north-1/*"
    }
  ]
}
```

5. Name the policy `polly-voice-s3-access` and choose **Create policy**.

The execution role supplies temporary credentials to the AWS SDK. Do not store
an access key or secret access key in Lambda code or environment variables.

![Lambda execution role](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.1.3.13.png)

![S3 IAM policy](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.20.png)

## 5. Create the DynamoDB history table

1. Open [Amazon DynamoDB](https://eu-north-1.console.aws.amazon.com/dynamodb/home?region=eu-north-1#tables) and choose **Create table**.
2. Configure:

| Setting | Value |
|---|---|
| Table name | `polly-voice-history` |
| Partition key | `PK` – String |
| Sort key | `SK` – String |
| Capacity mode | On-demand |
| Table class | DynamoDB Standard |
| Encryption | AWS owned key |

3. Enable **Point-in-time recovery** when recovery for the previous 35 days is
   required.
4. Wait until the table status becomes **Active**.
5. Open **Indexes → Create index** and create:

| Setting | Value |
|---|---|
| Index name | `EntityIndex` |
| Partition key | `entityId` – String |
| Projection | All attributes |

DynamoDB stores metadata and history. Audio and video bytes remain in S3; the
table stores only the S3 object key and related properties.

![DynamoDB table configuration](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.8.png)

![DynamoDB table settings](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.9.png)

![DynamoDB index](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.23.png)

## 6. Configure Lambda to write to DynamoDB

### 6.1. Grant DynamoDB permissions to Lambda

1. Open the Lambda execution role.
2. Choose **Add permissions → Create inline policy**.
3. Add:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:GetItem",
        "dynamodb:PutItem",
        "dynamodb:UpdateItem",
        "dynamodb:DeleteItem",
        "dynamodb:Query"
      ],
      "Resource": [
        "arn:aws:dynamodb:eu-north-1:<account-id>:table/polly-voice-history",
        "arn:aws:dynamodb:eu-north-1:<account-id>:table/polly-voice-history/index/EntityIndex"
      ]
    }
  ]
}
```

4. Name the policy `polly-voice-dynamodb-access`.

![DynamoDB IAM policy](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.27.png)

### 6.2. Configure environment variables

Open **Lambda → Configuration → Environment variables → Edit** and add:

| Variable | Example value | Purpose |
|---|---|---|
| `AWS_DYNAMODB_TABLE_NAME` | `polly-voice-history` | History table name |
| `AWS_S3_MEDIA_BUCKET` | `polly-voice-media-<account-id>-eu-north-1` | Media bucket name |
| `AWS_ENABLED` | `true` | Enable AWS integrations |

Do not create `AWS_REGION`; it is a reserved variable supplied by Lambda.

![Lambda environment variables](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.28.png)

### 6.3. Add the Lambda code

The Node.js runtime includes AWS SDK for JavaScript v3. The handler parses the
request, validates required values, builds a DynamoDB item, and sends a
`PutItemCommand`.

```javascript
import {
  DynamoDBClient,
  PutItemCommand
} from "@aws-sdk/client-dynamodb";

const dynamodb = new DynamoDBClient({
  region: process.env.AWS_REGION
});

const tableName = process.env.AWS_DYNAMODB_TABLE_NAME;

export const handler = async (event) => {
  try {
    if (!tableName) {
      throw new Error("AWS_DYNAMODB_TABLE_NAME is not configured.");
    }

    const body = typeof event.body === "string"
      ? JSON.parse(event.body)
      : event.body ?? event;

    const now = new Date().toISOString();
    const id = body._id ?? crypto.randomUUID();

    await dynamodb.send(new PutItemCommand({
      TableName: tableName,
      Item: {
        PK: { S: body.PK },
        SK: { S: body.SK },
        entityId: { S: body.entityId },
        _id: { S: id },
        userId: { S: body.userId },
        entityType: { S: body.entityType ?? "TTS" },
        status: { S: body.status ?? "COMPLETED" },
        textContent: { S: body.textContent },
        audioStorageKey: { S: body.audioStorageKey },
        createdAt: { S: body.createdAt ?? now },
        updatedAt: { S: now }
      }
    }));

    return {
      statusCode: 201,
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ message: "History item created.", id })
    };
  } catch (error) {
    console.error("Unable to write DynamoDB item", error);
    return {
      statusCode: 500,
      body: JSON.stringify({ message: error.message })
    };
  }
};
```

Choose **Deploy** after updating the code.

{{% notice warning %}}
Copy only the content inside a Markdown code block. Do not copy the word
`javascript` or the three backticks into `index.mjs`.
{{% /notice %}}

![Lambda code editor](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.31.png)

## 7. Connect Lambda to Amazon Polly

### 7.1. Grant Amazon Polly permissions

Create an inline policy named `polly-voice-polly-access` on the Lambda execution
role:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "polly:SynthesizeSpeech",
        "polly:DescribeVoices"
      ],
      "Resource": "*"
    }
  ]
}
```

![Amazon Polly policy](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.34.png)

### 7.2. Add the Polly integration

Import the Polly and S3 clients:

```javascript
import {
  PollyClient,
  SynthesizeSpeechCommand
} from "@aws-sdk/client-polly";
import {
  S3Client,
  PutObjectCommand
} from "@aws-sdk/client-s3";

const region = process.env.AWS_REGION;
const mediaBucket = process.env.AWS_S3_MEDIA_BUCKET;
const polly = new PollyClient({ region });
const s3 = new S3Client({ region });
```

The synthesis flow is:

1. Receive text, voice, and engine settings.
2. Call `SynthesizeSpeechCommand`.
3. Convert `AudioStream` into bytes.
4. Save the MP3 under `tts/<user-id>/<record-id>.mp3` in S3.
5. Store the resulting S3 key and metadata in DynamoDB.

```javascript
const response = await polly.send(new SynthesizeSpeechCommand({
  Text: text,
  VoiceId: voice,
  Engine: engine,
  OutputFormat: "mp3"
}));

const audioBytes = await response.AudioStream.transformToByteArray();
const audioStorageKey = `tts/${userId}/${recordId}.mp3`;

await s3.send(new PutObjectCommand({
  Bucket: mediaBucket,
  Key: audioStorageKey,
  Body: audioBytes,
  ContentType: "audio/mpeg"
}));
```

![Deploy Polly integration](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.35.png)

## 8. Connect Lambda to Amazon Transcribe

### 8.1. Grant Amazon Transcribe permissions

Create an inline policy named `polly-voice-transcribe-access`:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "transcribe:StartTranscriptionJob",
        "transcribe:GetTranscriptionJob",
        "transcribe:DeleteTranscriptionJob"
      ],
      "Resource": "*"
    }
  ]
}
```

![Amazon Transcribe policy](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.37.png)

### 8.2. Configure environment variables

Add:

| Variable | Example value | Purpose |
|---|---|---|
| `AWS_TRANSCRIBE_LANGUAGE_CODE` | `en-US` | Default transcription language |

![Transcribe environment variable](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.38.png)

### 8.3. Add the Transcribe integration

```javascript
import {
  TranscribeClient,
  StartTranscriptionJobCommand,
  GetTranscriptionJobCommand
} from "@aws-sdk/client-transcribe";

const transcribe = new TranscribeClient({
  region: process.env.AWS_REGION
});
```

To start a job, provide an S3 URI instead of sending a large file through API
Gateway or Lambda:

```javascript
await transcribe.send(new StartTranscriptionJobCommand({
  TranscriptionJobName: jobName,
  LanguageCode: languageCode,
  MediaFormat: mediaFormat,
  Media: {
    MediaFileUri: `s3://${mediaBucket}/${sourceStorageKey}`
  },
  OutputBucketName: mediaBucket,
  OutputKey: `transcripts/${jobName}.json`
}));
```

Use `GetTranscriptionJobCommand` to obtain `IN_PROGRESS`, `COMPLETED`, or
`FAILED` status. The frontend should poll the status endpoint until the job
finishes.

### 8.4. Validate the Transcribe connection

1. Upload a supported media file to `stt/<user-id>/uploads/` in S3.
2. Start a transcription job with the corresponding object key.
3. Open **Amazon Transcribe → Transcription jobs**.
4. Confirm that the output JSON appears under `transcripts/` in S3.

![Transcribe connection result](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.36.png)

{{% notice info %}}
Amazon Transcribe converts speech into text. Amazon Translate is a different
service that translates text between languages.
{{% /notice %}}

## 9. Create an API Gateway HTTP API

The project uses an **HTTP API**, not a REST API.

1. Open [Amazon API Gateway](https://eu-north-1.console.aws.amazon.com/apigateway/main/apis?region=eu-north-1) and choose **Create API**.
2. Under **HTTP API**, choose **Build**.
3. Enter `polly-voice-http-api-dev` as the API name.
4. Add a Lambda integration to `polly-voice` and use payload format version
   `2.0`.
5. Create the `$default` stage and enable **Auto-deploy**.
6. Create two proxy routes:

```text
ANY /
ANY /{proxy+}
```

7. Configure CORS:

| Setting | Value |
|---|---|
| Allowed origins | `http://localhost:5173` and the Amplify domain |
| Allowed methods | `GET`, `POST`, `DELETE`, `OPTIONS` |
| Allowed headers | `Authorization`, `Content-Type`, `X-User-Id` |
| Exposed headers | `Content-Disposition` |

The two proxy routes forward `/health` and `/api/v1/*` to the Express router in
Lambda; individual Express routes do not need to be recreated in API Gateway.

![Create API Gateway HTTP API](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.39.png)

![HTTP API stage](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.42.png)

![HTTP API routes](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.46.png)

![HTTP API CORS](https://hieuthaihcmut.github.io/fcj-workshop-template/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.47.png)

After these services are prepared, continue to **5.3.2 – Deploy the Backend with
AWS SAM**.
