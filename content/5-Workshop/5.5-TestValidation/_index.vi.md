---
title: "Kiểm thử và xác thực hệ thống"
date: 2026-07-30
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

Phần này xác nhận hệ thống Polly Voice hoạt động đúng sau khi frontend và backend
được triển khai. Quá trình kiểm thử bao gồm gửi request, xem log, kiểm tra metric,
thử các tình huống lỗi và đối chiếu kết quả thực tế với kết quả mong đợi.

## 1. Chuẩn bị thông tin kiểm thử

Chuẩn bị các giá trị sau:

| Thông tin | Ví dụ |
|---|---|
| Frontend URL | `https://<branch>.<app-id>.amplifyapp.com` |
| API URL | `https://<api-id>.execute-api.eu-north-1.amazonaws.com` |
| Cognito access token | Token nhận được sau khi đăng nhập |
| Lambda function | Function trong stack `polly-voice-api` |
| S3 bucket | `polly-voice-media-<account-id>-eu-north-1` |
| DynamoDB table | `polly-voice-history` |

Không đưa access token đầy đủ, access key, mật khẩu hoặc presigned URL còn hiệu
lực vào ảnh chụp báo cáo.

![Thông tin môi trường kiểm thử](/images/5-Workshop/5.5-TestValidation/01-test-environment.png)

## 2. Gửi request kiểm tra

### 2.1. Kiểm tra health endpoint

Mở trình duyệt hoặc Postman và gửi:

```text
GET <API_URL>/health
```

Kết quả mong đợi:

```json
{
  "status": "ok",
  "service": "polly-voice-backend",
  "region": "eu-north-1"
}
```

![Health request thành công](/images/5-Workshop/5.5-TestValidation/02-health-request.png)

### 2.2. Kiểm tra danh sách giọng đọc

```text
GET <API_URL>/api/v1/voices
```

Response phải có HTTP `200` và trả về danh sách voice mà frontend có thể hiển
thị. Kiểm tra voice và engine tương thích trước khi dùng cho TTS.

### 2.3. Kiểm tra Text-to-Speech

Đăng nhập trên frontend, nhập văn bản ngắn, chọn voice hợp lệ và chọn **Preview**
hoặc **Create Audio**. Trong Developer Tools → **Network**, kiểm tra request:

```text
POST <API_URL>/api/v1/tts/preview
Authorization: Bearer <ACCESS_TOKEN>
Content-Type: application/json
```

Kết quả mong đợi:

- HTTP `200` hoặc mã thành công mà API định nghĩa.
- Audio phát được trên trình duyệt.
- Chức năng tạo hoàn chỉnh trả về download URL có thời hạn.
- Object audio xuất hiện trong S3.
- Lịch sử xuất hiện trong DynamoDB và trên frontend.

![TTS request và response](/images/5-Workshop/5.5-TestValidation/03-tts-request.png)

### 2.4. Kiểm tra Speech-to-Text

1. Đăng nhập và chọn file audio/video hợp lệ.
2. Frontend yêu cầu presigned upload URL.
3. File được upload trực tiếp lên S3.
4. Frontend tạo Transcribe job.
5. Theo dõi đến khi job chuyển sang `COMPLETED`.
6. Kiểm tra transcript và chức năng tải kết quả.

Kết quả mong đợi: file không đi qua Lambda, Transcribe đọc file từ S3 và transcript
được hiển thị đúng trên frontend.

![STT hoàn tất](/images/5-Workshop/5.5-TestValidation/04-stt-result.png)

## 3. Xem log trong CloudWatch

1. Mở **AWS Lambda** → chọn Lambda của Polly Voice.
2. Chọn **Monitor** → **View CloudWatch logs**.
3. Mở log stream mới nhất.
4. Tìm request vừa gửi theo timestamp hoặc request ID.
5. Kiểm tra các bản ghi `START`, `END`, `REPORT` và log của ứng dụng.

Log cần cho biết request đã đến Lambda và không có `Runtime.Unknown`,
`AccessDeniedException`, timeout hoặc lỗi module.

Đối với API Gateway, mở stage `$default` và kiểm tra access log nếu đã bật.

![CloudWatch log stream](/images/5-Workshop/5.5-TestValidation/05-cloudwatch-logs.png)

{{% notice warning %}}
Không ghi access token, nội dung nhạy cảm hoặc presigned URL đầy đủ vào log ứng
dụng. Chỉ dùng request ID, user ID đã rút gọn và mã lỗi để truy vết.
{{% /notice %}}

## 4. Kiểm tra metric

### 4.1. Lambda

Trong **Lambda** → **Monitor**, kiểm tra:

- **Invocations:** tăng khi gửi request.
- **Errors:** bằng `0` với luồng thành công.
- **Duration:** thấp hơn timeout `60` giây.
- **Throttles:** bằng `0`.
- **Concurrent executions:** không vượt quota.

### 4.2. API Gateway

Trong **API Gateway** → **Monitor**, kiểm tra:

- **Count:** có request mới.
- **2XX:** tăng với request thành công.
- **4XX:** chỉ tăng khi cố ý kiểm thử request sai hoặc thiếu token.
- **5XX:** không tăng trong luồng bình thường.
- **Latency** và **IntegrationLatency:** nằm trong mức chấp nhận được.

### 4.3. Các dịch vụ lưu trữ

- S3 có object media đúng prefix và không public.
- DynamoDB có item đúng `PK`, `SK`, `entityId`.
- Transcribe job kết thúc ở `COMPLETED`.
- Không có alarm ở trạng thái `ALARM` sau luồng thành công.

![CloudWatch metrics](/images/5-Workshop/5.5-TestValidation/06-cloudwatch-metrics.png)

## 5. Kiểm thử lỗi

Thực hiện có kiểm soát các trường hợp sau:

| Trường hợp | Cách kiểm thử | Kết quả mong đợi |
|---|---|---|
| Thiếu token | Gọi route lịch sử không có `Authorization` | HTTP `401`; không trả dữ liệu |
| Token sai/hết hạn | Gửi bearer token không hợp lệ | HTTP `401` hoặc `403` |
| CORS sai | Gọi API từ origin chưa được cho phép | Trình duyệt chặn request; log hỗ trợ chẩn đoán |
| Body không hợp lệ | Bỏ trống text hoặc gửi sai kiểu dữ liệu | HTTP `400`; thông báo rõ ràng |
| Voice/engine không tương thích | Chọn tổ hợp không được Polly hỗ trợ | HTTP `400`; không tạo bản ghi hoàn tất giả |
| File không hợp lệ | Upload sai định dạng hoặc vượt giới hạn cho phép | HTTP `400`/`413`; frontend thông báo lỗi |
| Không có quyền AWS | Dùng môi trường thử thiếu một quyền IAM | HTTP `5xx`; CloudWatch có `AccessDenied` |
| Không tìm thấy bản ghi | Gọi ID không tồn tại | HTTP `404` |
| Route không tồn tại | Gọi `/api/v1/not-found` | HTTP `404` |

Sau mỗi test lỗi, khôi phục policy/cấu hình đúng trước khi tiếp tục. Không thực
hiện test phá hoại trên dữ liệu quan trọng.

![Kiểm thử lỗi xác thực](/images/5-Workshop/5.5-TestValidation/07-auth-error.png)

## 6. Kết quả mong đợi

Hệ thống được xem là đạt khi:

- Frontend tải qua HTTPS và không có lỗi nghiêm trọng trong Console.
- Cognito đăng ký, xác nhận email, đăng nhập và đăng xuất thành công.
- Health API trả về HTTP `200`.
- TTS preview phát được; audio hoàn chỉnh tải xuống được.
- STT upload trực tiếp lên S3 và trả về transcript.
- Người dùng chỉ xem và xóa được dữ liệu thuộc tài khoản của mình.
- S3 vẫn private; file được truy cập bằng presigned URL có thời hạn.
- DynamoDB lưu đúng lịch sử TTS/STT.
- Lambda và API Gateway không có lỗi `5XX` trong luồng hợp lệ.
- CloudWatch ghi đủ log và metric để truy vết.
- Các request lỗi trả về mã `4XX` phù hợp, không làm Lambda dừng bất thường.

## 7. Bảng ghi nhận kết quả

| Test ID | Nội dung | Kết quả mong đợi | Kết quả thực tế | Trạng thái |
|---|---|---|---|---|
| TC-01 | Health API | HTTP 200 | Điền sau kiểm thử | Pass/Fail |
| TC-02 | Đăng nhập Cognito | Nhận phiên hợp lệ | Điền sau kiểm thử | Pass/Fail |
| TC-03 | TTS Preview | Phát được audio | Điền sau kiểm thử | Pass/Fail |
| TC-04 | Tạo và tải MP3 | Tải file thành công | Điền sau kiểm thử | Pass/Fail |
| TC-05 | STT | Nhận transcript | Điền sau kiểm thử | Pass/Fail |
| TC-06 | Lịch sử | Đúng dữ liệu người dùng | Điền sau kiểm thử | Pass/Fail |
| TC-07 | Thiếu token | HTTP 401 | Điền sau kiểm thử | Pass/Fail |
| TC-08 | Input sai | HTTP 400 | Điền sau kiểm thử | Pass/Fail |
| TC-09 | Route sai | HTTP 404 | Điền sau kiểm thử | Pass/Fail |
| TC-10 | CloudWatch | Có log và metric | Điền sau kiểm thử | Pass/Fail |

![Kết quả kiểm thử tổng hợp](/images/5-Workshop/5.5-TestValidation/08-test-summary.png)

