---
title: "Cấu hình dịch vụ AWS cho Backend"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.4.1. </b> "
---

Phần này chuẩn bị các dịch vụ mà backend Polly Voice sử dụng **trước khi triển
khai mã nguồn**. Các tài nguyên được tạo và kiểm tra chủ yếu bằng AWS Management
Console tại Region **Europe (Stockholm) – `eu-north-1`**.

{{% notice info %}}
AWS SAM ở phần tiếp theo có thể tự tạo lại API Gateway, Lambda, S3 và DynamoDB.
Các bước dưới đây giúp hiểu, thử nghiệm và ghi nhận cách từng dịch vụ hoạt động.
Khi triển khai chính thức, chỉ nên chọn **một nguồn quản lý tài nguyên** để tránh
tạo trùng: Console cho bản thử nghiệm hoặc SAM/CloudFormation cho hệ thống chính.
{{% /notice %}}

## 1. Tạo Lamda function

1. Truy cập [AWS Lambda](https://eu-north-1.console.aws.amazon.com/lambda/home?region=eu-north-1#/functions) và chọn **Create function**.

![Lambda create function](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/createfunction.png)

2. Tại trang **Create function**, chọn **Author from scratch**. Đặt tên funtion, ví dụ `polly-voice`. Chọn runtime là Nodejs 2x.x . Chọn architecture `arm64` để tiết kiệm chi phí. Cuối cùng là nhấn **Create function**.

![Lambda author from scratch](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.2.png)

## 2. Tạo S3 bucket lưu media

1. Truy cập Amazon [S3](https://eu-north-1.console.aws.amazon.com/s3/home?region=eu-north-1) và chọn **Create bucket**.

![S3 create bucket](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.3.png)

2. Chọn **Account Regional namespace** trong **Bucket namespace**. Sau đó nhập tên dự án, ví dụ: `polly-voice-media-<account-id>-eu-north-1`. Giữ nguyên **Block all public access**. Bật **Bucket Versioning** nếu muốn khôi phục file. Chọn **Server-side encryption with Amazon S3 managed keys (SSE-S3)**. Cuối cùng nhấn **Create bucket**.

![S3 bucket configuration](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.4.png)
![Bucket versioning](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.5.png)
![S3 bucket encryption](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.6.png)

## 3. Cấu hình Lamda Trigger cho S3 bucket
1. Tại trang polly-voice Lambda function, chọn **Add trigger**.
![S3 trigger](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.10.png)
2. Tìm và chọn **S3** trong danh sách trigger. Sau đó chọn **Add**.
![S3 trigger configuration](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.11.png)
3. Tại trang cấu hình trigger. Chọn bucket nguồn: `polly-voice-media-<account-id>-eu-north-1`. Chọn **All object create events**. Đánh dấu vào ô Xác nhận **I acknowledge that this may result in the invocation of my function when objects are created in the bucket**. Cuối cùng nhấn **Add**.
![S3 trigger configuration](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.12.png)

## 4. Tạo IAM Policy cho Lambda
1. Tại trang polly-voice Lambda function, chọn **Configuration**. Sau đó chọn **Permissions**. Chọn vào role gắn với function.
![Lambda permissions](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.1.3.13.png)
2. Tại trang IAM role, chọn **Add permissions**và chọn **Create inline policy**.
![Lambda inline policy](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.14.png)
3. Tại trang **Create policy**, chọn **Choose a service** và tìm kiếm **S3**. Chọn **S3**.
![S3 service](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.15.png)
4. Tại ô tìm kiếm **Filter actions**, nhập `GetObject` và chọn **GetObject**. Tương tự cho `DeleteObject`. Sau đó chọn **Add resources**.

![S3 actions](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.19.png)
5. Chọn **Add ARN** trong mục `object` trong **Resorces**. 
![S3 resources](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.17.png)
6. Tại trang **Add ARN**, chọn bucket nguồn: `polly-voice-media-<account-id>-eu-north-1`. Chọn **object** và nhập `*` để áp dụng cho tất cả object trong bucket. Cuối cùng nhấn **Add**.
![S3 add ARN](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.18.png)
7. Chọn **+Add more permissions** và làm tương tự bước trên cho hành động `PutObject`. Sau đó nhấn **Next**.
![S3 policy review](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.16.png)
8. Nhập tên policy, ví dụ: `polly-voice-s3-access`. Cuối cùng nhấn **Create policy**.
![S3 policy name](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.20.png)

## 5. Tạo DynamoDB table lưu lịch sử

1. Mở [Amazon DynamoDB](https://eu-north-1.console.aws.amazon.com/dynamodb/home?region=eu-north-1#tables) và chọn **Create table**.
![DynamoDB create table](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.7.png)
2. Nhập các thông tin cho Table name: `polly-voice-history`. Partition key: `PK`, kiểu **String**. Sort key: `SK`, kiểu **String**. 
![DynamoDB table configuration](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.8.png)

3. Chọn vào **Customize settings** để cấu hình thêm. Chọn **DynamoDB Standard** cho **Table class** và **On-demand** cho **Read/write capacity mode**. Bật **Point-in-time recovery** để có thể khôi phục dữ liệu trong 35 ngày. Bật **Encryption at rest** với **AWS owned key**. Cuối cùng nhấn **Create table**.
![DynamoDB table settings](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.9.png)

4. Chờ đến khi trạng thái của bảng chuyển thành **Active**. Sau đó mở table `polly-voice-history`.
![DynamoDB table active](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.21.png)

5. Chọn tabs **Indexes** và chọn **Create index**. 
![Index configuration](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.22.png)
6. Nhập `EntityIndex` cho **Index name**. Nhập `entityID` cho **Partition key**. Các thuộc tính khác giữ nguyên. Cuối cùng nhấn **Create index**.
![Index configuration](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.23.png)

## 6. Tạo Lambda function để ghi dữ liệu vào DynamoDB

### 6.1. Cấp quyền DynamoDB cho Lambda

1. Mở [AWS Lambda](https://eu-north-1.console.aws.amazon.com/lambda/home?region=eu-north-1#/functions) và chọn function `polly-voice`.
![Lambda function](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.24.png)

2. Chọn tabs **Configuration**, sau đó chọn **Permissions**.Trong phần **Execution role**, chọn tên IAM role đang được gắn với Lambda.
![Lambda permissions](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.25.png)

3. Tại trang IAM role, chọn **Add permissions** → **Create inline policy**.
![Lambda inline policy](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.26.png)

4. Chọn tab **JSON** và nhập policy sau:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PollyVoiceDynamoDBAccess",
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

![Lambda DynamoDB policy](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.27.png)
5. Chọn **Next**, đặt tên `polly-voice-dynamodb-access` và chọn **Create policy**.
![IAM policy cho DynamoDB](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.33.png)

### 6.2. Cấu hình biến môi trường
1. Trong Lambda function `polly-voice`, chọn tabs **Configuration**. Sau đó chọn **Environment variables**.
![Lambda environment variables](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.28.png)
2. Sau đó chọn **Edit** và thêm biến môi trường:
| Tên biến | Giá trị ví dụ| Ý nghĩa |
|---|---|---|
| `AWS_DYNAMODB_TABLE_NAME` | `polly-voice-history` | Tên DynamoDB table lưu lịch sử TTS và STT. |
| `AWS_S3_MEDIA_BUCKET` | `polly-voice-media-<account-id>-eu-north-1` | Tên S3 bucket lưu audio, media đầu vào và transcript. |
| `AWS_ENABLED` | `true` | Bật/tắt backend. |

Sau khi thêm xong, nhấn **Save**.

![Lambda environment variables saved](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.29.png)

### 6.3. Viết mã Lambda

1. Mở Lambda function `polly-voice`. Chọn tab **Code**.Trong phần **Code source**, mở file `index.mjs`.
![Lambda code editor](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.30.png)
2. Thay nội dung mặc định bằng đoạn mã sau:

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
      throw new Error(
        "AWS_DYNAMODB_TABLE_NAME is not configured."
      );
    }

    const body =
      typeof event.body === "string"
        ? JSON.parse(event.body)
        : event.body ?? event;

    const requiredFields = [
      "PK",
      "SK",
      "entityId",
      "userId",
      "textContent",
      "audioStorageKey"
    ];

    const missingFields = requiredFields.filter(
      (field) => !body[field]
    );

    if (missingFields.length > 0) {
      return {
        statusCode: 400,
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify({
          message: "Missing required fields.",
          missingFields
        })
      };
    }

    const now = new Date().toISOString();

    const command = new PutItemCommand({
      TableName: tableName,

      Item: {
        PK: {
          S: body.PK
        },
        SK: {
          S: body.SK
        },
        entityId: {
          S: body.entityId
        },
        _id: {
          S: body._id ?? crypto.randomUUID()
        },
        userId: {
          S: body.userId
        },
        entityType: {
          S: "TTS"
        },
        status: {
          S: body.status ?? "completed"
        },
        textContent: {
          S: body.textContent
        },
        language: {
          S: body.language ?? "en-US"
        },
        voice: {
          S: body.voice ?? "Joanna"
        },
        engine: {
          S: body.engine ?? "neural"
        },
        audioStorageKey: {
          S: body.audioStorageKey
        },
        contentType: {
          S: body.contentType ?? "audio/mpeg"
        },
        audioFileSize: {
          N: String(body.audioFileSize ?? 0)
        },
        characterCount: {
          N: String(body.textContent.length)
        },
        createdAt: {
          S: body.createdAt ?? now
        },
        updatedAt: {
          S: now
        }
      },

      ReturnValues: "ALL_OLD"
    });

    const result = await dynamodb.send(command);
    const overwritten = Boolean(result.Attributes);

    console.log("DynamoDB write completed", {
      tableName,
      PK: body.PK,
      SK: body.SK,
      overwritten
    });

    return {
      statusCode: overwritten ? 200 : 201,
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        message: overwritten
          ? "The existing item was overwritten successfully."
          : "A new item was created successfully.",
        data: {
          PK: body.PK,
          SK: body.SK,
          entityId: body.entityId,
          audioStorageKey: body.audioStorageKey
        }
      })
    };
  } catch (error) {
    console.error("Unable to write DynamoDB item", {
      name: error.name,
      message: error.message
    });

    return {
      statusCode: 500,
      headers: {
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        message: "Unable to write the history item.",
        error: error.message
      })
    };
  }
};
```

![Lambda code editor](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.31.png)

3. Chọn **Deploy** để lưu phiên bản mã nguồn mới.

![Deploy Lambda code](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.30.png)
## 7. Kết nối Lambda với Amazon Polly
### 7.1. Cấp quyền Amazon Polly

1. Mở [AWS Lambda](https://eu-north-1.console.aws.amazon.com/lambda/home?region=eu-north-1#/functions) và chọn function `polly-voice`.
![Lambda function](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.24.png)

2. Chọn tabs **Configuration**, sau đó chọn **Permissions**.Trong phần **Execution role**, chọn tên IAM role đang được gắn với Lambda.
![Lambda permissions](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.25.png)
3. Tại trang IAM role, chọn **Add permissions** → **Create inline policy**.
![Lambda inline policy](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.26.png)
4. Chọn tab **JSON** và thêm policy:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowPollyVoiceSynthesis",
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
![Lambda Polly policy](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.32.png)

5. Chọn **Next**, đặt tên `polly-voice-polly-access` và chọn **Create policy**.

![IAM policy cho Amazon Polly](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.34.png)
### 7.2. Bổ sung mã kết nối Polly

Trong file `index.mjs`, bổ sung các import:

```javascript
import {
  PollyClient,
  SynthesizeSpeechCommand
} from "@aws-sdk/client-polly";

import {
  S3Client,
  PutObjectCommand
} from "@aws-sdk/client-s3";
```

Khởi tạo client bằng Region và bucket đã cấu hình:

```javascript
const region = process.env.AWS_REGION;
const mediaBucket = process.env.AWS_S3_MEDIA_BUCKET;

const polly = new PollyClient({ region });
const s3 = new S3Client({ region });
```

Thêm hàm tổng hợp giọng nói và lưu audio vào S3:

```javascript
async function synthesizeAndStoreAudio({
  text,
  userId,
  recordId,
  voice = "Joanna",
  engine = "neural"
}) {
  if (!mediaBucket) {
    throw new Error("AWS_S3_MEDIA_BUCKET is not configured.");
  }

  const pollyResponse = await polly.send(
    new SynthesizeSpeechCommand({
      Text: text,
      VoiceId: voice,
      Engine: engine,
      OutputFormat: "mp3"
    })
  );

  if (!pollyResponse.AudioStream) {
    throw new Error("Amazon Polly did not return an audio stream.");
  }

  const audioBytes =
    await pollyResponse.AudioStream.transformToByteArray();

  const audioStorageKey =
    `tts/${userId}/${recordId}.mp3`;

  await s3.send(
    new PutObjectCommand({
      Bucket: mediaBucket,
      Key: audioStorageKey,
      Body: audioBytes,
      ContentType: "audio/mpeg"
    })
  );

  return {
    audioStorageKey,
    audioFileSize: audioBytes.byteLength,
    contentType: "audio/mpeg"
  };
}
```

Trong `handler`, gọi hàm trên trước khi tạo `PutItemCommand`:

```javascript
const recordId = body._id ?? crypto.randomUUID();

const audio = await synthesizeAndStoreAudio({
  text: body.textContent,
  userId: body.userId,
  recordId,
  voice: body.voice ?? "Joanna",
  engine: body.engine ?? "neural"
});
```

Sau đó thay các thuộc tính audio trong DynamoDB bằng kết quả thực tế:

```javascript
audioStorageKey: {
  S: audio.audioStorageKey
},
contentType: {
  S: audio.contentType
},
audioFileSize: {
  N: String(audio.audioFileSize)
}
```

Đồng thời xóa `audioStorageKey` khỏi mảng `requiredFields`, vì đường dẫn này được
Lambda tạo sau khi upload audio chứ không nhận trực tiếp từ frontend.

6. Chọn **Deploy** để lưu phiên bản mã nguồn mới.
![Deploy Lambda code](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.35.png)

## 8. Kết nối Lambda với Amazon Transcribe
### 8.1. Cấp quyền Amazon Transcribe

1. Mở [AWS Lambda](https://eu-north-1.console.aws.amazon.com/lambda/home?region=eu-north-1#/functions) và chọn function `polly-voice`.
![Lambda function](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.24.png)

2. Chọn tabs **Configuration**, sau đó chọn **Permissions**.Trong phần **Execution role**, chọn tên IAM role đang được gắn với Lambda.
![Lambda permissions](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.25.png)
3. Tại trang IAM role, chọn **Add permissions** → **Create inline policy**.
![Lambda inline policy](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.26.png)
4. Chọn tab **JSON** và thêm policy:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowTranscriptionJobs",
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
Sau đó chọn **Next**.
![Lambda Transcribe policy](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.37.png)

5. Đặt tên policy là `polly-voice-transcribe-access`.
![Lambda Transcribe policy](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.36.png)
### 8.2. Cấu hình biến môi trường

Trong Lambda → **Configuration** → **Environment variables** -> **Edit**, sau đó thêm các biến môi trường:

| Tên biến | Giá trị ví dụ | Ý nghĩa |
|---|---|---|
| `AWS_TRANSCRIBE_LANGUAGE_CODE` | `en-US` | Ngôn ngữ mặc định |
![Lambda environment variables](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.38.png)


### 8.3. Bổ sung mã kết nối Transcribe

Thêm import vào `index.mjs`:

```javascript
import {
  TranscribeClient,
  StartTranscriptionJobCommand,
  GetTranscriptionJobCommand
} from "@aws-sdk/client-transcribe";
```

Khởi tạo client:

```javascript
const transcribe = new TranscribeClient({
  region: process.env.AWS_REGION
});

const defaultLanguageCode =
  process.env.AWS_TRANSCRIBE_LANGUAGE_CODE ?? "en-US";
```

Thêm hàm tạo transcription job:

```javascript
async function startTranscription({
  jobName,
  sourceStorageKey,
  languageCode = defaultLanguageCode,
  mediaFormat = "mp3"
}) {
  if (!mediaBucket) {
    throw new Error("AWS_S3_MEDIA_BUCKET is not configured.");
  }

  const resultStorageKey =
    `transcripts/${jobName}.json`;

  await transcribe.send(
    new StartTranscriptionJobCommand({
      TranscriptionJobName: jobName,
      LanguageCode: languageCode,
      MediaFormat: mediaFormat,
      Media: {
        MediaFileUri:
          `s3://${mediaBucket}/${sourceStorageKey}`
      },
      OutputBucketName: mediaBucket,
      OutputKey: resultStorageKey
    })
  );

  return {
    jobName,
    sourceStorageKey,
    resultStorageKey,
    status: "IN_PROGRESS"
  };
}
```

Thêm hàm kiểm tra trạng thái:

```javascript
async function getTranscriptionStatus(jobName) {
  const response = await transcribe.send(
    new GetTranscriptionJobCommand({
      TranscriptionJobName: jobName
    })
  );

  const job = response.TranscriptionJob;

  return {
    jobName,
    status: job?.TranscriptionJobStatus,
    transcriptFileUri:
      job?.Transcript?.TranscriptFileUri,
    failureReason: job?.FailureReason
  };
}
```
Sau đó chọn **Deploy** để lưu phiên bản mã nguồn mới.


Frontend cần upload media trực tiếp lên S3 bằng presigned URL trước khi gọi API
tạo job. Cách này tránh truyền file lớn qua API Gateway và Lambda.

### 8.4. Kiểm tra kết nối Transcribe

1. Upload file `transcribe-test.mp3` vào:

```text
stt/test-user/uploads/transcribe-test.mp3
```

2. Tạo test event:

```json
{
  "action": "START_TRANSCRIPTION",
  "jobName": "polly-voice-transcribe-test",
  "sourceStorageKey": "stt/test-user/uploads/transcribe-test.mp3",
  "languageCode": "en-US",
  "mediaFormat": "mp3"
}
```

3. Trong `handler`, điều hướng event đến hàm tương ứng:

```javascript
if (body.action === "START_TRANSCRIPTION") {
  const data = await startTranscription(body);

  return {
    statusCode: 202,
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ data })
  };
}

if (body.action === "GET_TRANSCRIPTION_STATUS") {
  const data =
    await getTranscriptionStatus(body.jobName);

  return {
    statusCode: 200,
    headers: {
      "Content-Type": "application/json"
    },
    body: JSON.stringify({ data })
  };
}
```

Đặt hai khối điều kiện trên ngay sau khi parse `body` và trước phần kiểm tra
`requiredFields` của TTS.

4. Chọn **Deploy**, sau đó chạy event tạo job.
5. Mở Amazon Transcribe → **Transcription jobs** và kiểm tra job vừa tạo.
6. Khi job hoàn tất, chạy event:

```json
{
  "action": "GET_TRANSCRIPTION_STATUS",
  "jobName": "polly-voice-transcribe-test"
}
```

7. Kết quả mong đợi:

```json
{
  "data": {
    "jobName": "polly-voice-transcribe-test",
    "status": "COMPLETED",
    "transcriptFileUri": "<temporary-result-url>"
  }
}
```

8. Kiểm tra transcript tại:

```text
s3://polly-voice-media-<account-id>-eu-north-1/transcripts/polly-voice-transcribe-test.json
```

![Kết quả kết nối Amazon Transcribe](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.36.png)

{{% notice info %}}
Transcribe không phải Amazon Translate. Transcribe chuyển giọng nói thành văn
bản; Translate dịch văn bản giữa các ngôn ngữ.
{{% /notice %}}

Kết nối thành công khi Lambda tạo được job, job chuyển sang `COMPLETED`, file kết
quả xuất hiện trong S3 và CloudWatch không có lỗi `AccessDenied`.

## 9. Tạo API Gateway HTTP API

Dự án sử dụng **HTTP API**, không phải REST API.

1. Mở [Amazon API Gateway](https://eu-north-1.console.aws.amazon.com/apigateway/home?region=eu-north-1#/apis). Sau đó chọn **Create API**.

![API Gateway create API](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.39.png)
2. Tại **HTTP API**, chọn **Build**.
![API Gateway HTTP API build](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.40.png)
3. Đặt tên API name: `polly-voice-http-api-dev`.
![API Gateway HTTP API name](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.41.png)
4. Trong **Integrations**, chọn **Add integration** và chọn **Lambda** và chọn **Lambda function**.
5. Sau đó chọn **Next** tạo stage `$default` và bật **Auto-deploy**.
![API Gateway HTTP API stage](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.42.png)
6. Tạo hai route proxy bằng cách chọn **Create** trong hộp **Routes**. Nhập:
   - `ANY /`
   - `ANY /{proxy+}`
![API Gateway HTTP API routes](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.44.png)
![API Gateway HTTP API routes](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.45.png)
![API Gateway HTTP API routes](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.46.png)
7. Chọn vào tabs **CORS**, và chọn vào **Config** cấu hình:
   - Allowed origins: `http://localhost:5173` và domain Amplify.
   - Allowed methods: `GET`, `POST`, `DELETE`, `OPTIONS`.
   - Allowed headers: `Authorization`, `Content-Type`, `X-User-Id`.
   - Exposed headers: `Content-Disposition`.
![API Gateway HTTP API CORS](/images/5-Workshop/5.3-DeployBackend/5.3.1-services/5.3.1.47.png)
Hai route proxy chuyển toàn bộ endpoint `/health` và `/api/v1/*` cho Express
router trong Lambda xử lý; không cần hard-code từng route trên API Gateway.

