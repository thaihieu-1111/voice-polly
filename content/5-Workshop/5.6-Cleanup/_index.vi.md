---
title: "Dọn dẹp tài nguyên"
date: 2026-07-30
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

Sau khi hoàn tất kiểm thử, xóa các tài nguyên không còn sử dụng để tránh phát
sinh chi phí. Thực hiện trong Region **Europe (Stockholm) – `eu-north-1`** và
kiểm tra kỹ tên tài nguyên trước khi xóa.

{{% notice danger %}}
Dọn dẹp sẽ xóa API, Lambda, dữ liệu media, transcript và lịch sử. Tải về dữ liệu
cần giữ trước khi tiếp tục. Không xóa tài nguyên đang được ứng dụng khác sử dụng.
{{% /notice %}}

## 1. Ghi nhận tài nguyên trước khi xóa

Mở CloudFormation stack `polly-voice-api` → **Resources** và **Outputs**, ghi lại:

- API Gateway HTTP API.
- Lambda function và execution role.
- S3 media bucket.
- DynamoDB history table.
- CloudWatch log group và alarm.
- Cognito User Pool/App Client.
- Amplify app nếu cũng dừng frontend.


![Danh sách tài nguyên cần xóa](/images/5-Workshop/5.6-Cleanup/01-resource-inventory.png)

## 2. Xóa dữ liệu trong S3 bucket

Stack sử dụng `DeletionPolicy: Retain`, vì vậy bucket có thể được giữ lại sau khi
xóa stack. Bucket cũng không thể xóa khi vẫn còn object hoặc version.

1. Mở **Amazon S3** → chọn media bucket.
2. Tải về media hoặc transcript cần lưu.
3. Chọn **Empty**.
4. Nhập chuỗi xác nhận và hoàn tất.
5. Nếu bật versioning, kiểm tra tất cả version và delete marker đã được xóa.

Chưa xóa bucket ở bước này nếu muốn CloudFormation gỡ stack trước; chỉ làm rỗng.

![Làm rỗng S3 bucket](/images/5-Workshop/5.6-Cleanup/02-empty-s3-bucket.png)

## 3. Xóa CloudWatch alarm

1. Mở **CloudWatch** → **Alarms** → **All alarms**.
2. Chọn alarm của Lambda và API Gateway thuộc Polly Voice.
3. Chọn **Actions** → **Delete** và xác nhận.

Dashboard hoặc log group tạo thủ công không nhất thiết được CloudFormation xóa.
Xóa chúng nếu không còn dùng.

![Xóa CloudWatch alarm](/images/5-Workshop/5.6-Cleanup/03-delete-alarms.png)


## 4. Xóa CloudFormation stack

1. Mở **AWS CloudFormation** → **Stacks**.
2. Chọn `polly-voice-api`.
3. Kiểm tra lần cuối tab **Resources**.
4. Chọn **Delete** → **Delete stack**.
5. Theo dõi tab **Events** đến khi stack biến mất hoặc hoàn tất xóa.

CloudFormation sẽ xóa các tài nguyên do stack quản lý như API Gateway và Lambda.
S3 bucket và DynamoDB table của dự án có thể vẫn còn do chính sách `Retain`.

![Xóa CloudFormation stack](/images/5-Workshop/5.6-Cleanup/04-delete-stack.png)

Nếu stack ở trạng thái `DELETE_FAILED`, mở **Events**, tìm tài nguyên lỗi, xử lý
nguyên nhân (thường là bucket chưa rỗng hoặc quyền xóa chưa đủ) rồi chọn **Retry
delete**.

## 5. Xóa các tài nguyên được giữ lại

### 5.1. Xóa S3 bucket

1. Trở lại S3 và xác nhận bucket trống.
2. Chọn bucket → **Delete**.
3. Nhập chính xác tên bucket và xác nhận.

![Xóa S3 bucket](/images/5-Workshop/5.6-Cleanup/05-delete-bucket.png)

### 5.2. Xóa DynamoDB table

1. Mở **DynamoDB** → **Tables**.
2. Chọn `polly-voice-history`.
3. Tạo backup nếu cần giữ lịch sử.
4. Chọn **Delete** và xác nhận.

![Xóa DynamoDB table](/images/5-Workshop/5.6-Cleanup/06-delete-table.png)

## 6. Xóa log group còn lại

1. Mở **CloudWatch** → **Logs** → **Log groups**.
2. Tìm `/aws/lambda/<function-name>` và log group API Gateway.
3. Chọn các log group chỉ thuộc workshop → **Delete**.

Nếu cần giữ log phục vụ báo cáo, tải hoặc xuất log trước khi xóa.

## 7. Xóa tài nguyên tạo thủ công

Nếu ở phần phát triển đã tạo riêng ngoài SAM/CloudFormation, xóa:

- Lambda `polly-voice-api-dev`.
- API Gateway `polly-voice-http-api-dev`.
- IAM inline policy và execution role không còn được sử dụng.
- Transcribe test jobs không còn cần thiết.

IAM role chỉ xóa được khi đã gỡ khỏi Lambda và bỏ các policy liên quan.

## 8. Tùy chọn: xóa Cognito và Amplify

Chỉ thực hiện khi không tiếp tục dùng frontend:

1. Amplify → chọn app → **App settings** → **Delete app**.
2. Cognito → **User pools** → chọn User Pool → **Delete**.

Việc xóa Cognito User Pool sẽ xóa toàn bộ tài khoản người dùng và không thể hoàn
tác. Nếu chỉ dừng backend tạm thời, có thể giữ Cognito và Amplify.

## 9. Xác nhận hoàn tất

Kiểm tra:

- Stack `polly-voice-api` không còn.
- API Gateway và Lambda của stack không còn.
- S3 bucket và DynamoDB table đã được xóa hoặc được chủ động giữ lại.
- Không còn alarm hoặc log group không cần thiết.
- Không còn Transcribe job đang chạy.
- Amplify/Cognito đã được xử lý theo phạm vi mong muốn.
- **Billing and Cost Management** không còn chi phí bất thường.

![Hoàn tất dọn dẹp](/images/5-Workshop/5.6-Cleanup/07-cleanup-complete.png)


