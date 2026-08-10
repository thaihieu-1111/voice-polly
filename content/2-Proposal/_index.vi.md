+++
title = '2. Bản đề xuất'
weight = 2

[params]
collapsibleMenu = true
+++

## Polly Voice

Ứng dụng xử lý giọng nói sử dụng AWS Serverless

---

## 1. Tổng quan dự án

### Mục tiêu

Polly Voice là một ứng dụng web được xây dựng nhằm cung cấp hai chức năng xử lý giọng nói:

* **Text-to-Speech (TTS):** Chuyển đổi văn bản thành giọng nói bằng **Amazon Polly**.
* **Speech-to-Text (STT):** Chuyển đổi giọng nói thành văn bản bằng **Amazon Transcribe**.

Người dùng có thể đăng nhập vào hệ thống, nhập văn bản để tạo tệp âm thanh hoặc tải lên tệp âm thanh để nhận dạng nội dung. Kết quả có thể được nghe trực tiếp, tải xuống hoặc xem lại trong lịch sử sử dụng.

Ứng dụng được phát triển hoàn toàn theo kiến trúc Serverless trên nền tảng Amazon Web Services (AWS), giúp giảm chi phí vận hành, dễ dàng mở rộng và không cần quản lý máy chủ.

---

## 2. Vấn đề cần giải quyết

### Thực trạng

Hiện nay nhiều người có nhu cầu chuyển đổi giữa văn bản và giọng nói phục vụ học tập, đọc tài liệu, tạo nội dung, ghi chú cuộc họp hoặc hỗ trợ người khiếm thị. Tuy nhiên:

* Nhiều dịch vụ Text-to-Speech và Speech-to-Text yêu cầu trả phí.
* Một số nền tảng không hỗ trợ đầy đủ nhiều ngôn ngữ hoặc giọng đọc.
* Việc xây dựng hệ thống xử lý giọng nói truyền thống cần triển khai và quản lý máy chủ, gây tốn thời gian và chi phí.

### Giải pháp

Polly Voice sử dụng:

* **Amazon Polly** để chuyển đổi văn bản thành giọng nói.
* **Amazon Transcribe** để chuyển đổi giọng nói thành văn bản.

Hệ thống áp dụng kiến trúc Serverless gồm:

* Amazon Cognito quản lý người dùng.
* React chạy trên AWS Amplify.
* API Gateway tiếp nhận yêu cầu từ frontend.
* AWS Lambda xử lý nghiệp vụ.
* Amazon Polly thực hiện Text-to-Speech.
* Amazon Transcribe thực hiện Speech-to-Text.
* Amazon S3 lưu trữ các tệp âm thanh.
* Amazon DynamoDB lưu lịch sử chuyển đổi.

Người dùng chỉ cần đăng nhập, sau đó có thể nhập văn bản để tạo giọng nói hoặc tải lên tệp âm thanh để nhận dạng nội dung.

### Lợi ích

* Không cần quản lý server.
* Chi phí vận hành thấp.
* Dễ dàng mở rộng khi số lượng người dùng tăng.
* Tốc độ xử lý nhanh.
* Hỗ trợ cả hai chức năng Text-to-Speech và Speech-to-Text.
* Có thể tái sử dụng cho nhiều ứng dụng AI hoặc học tập trong tương lai.

---

## 3. Kiến trúc giải pháp

![Mô tả ảnh](https://super-chickens-aws.github.io/dinhhhiuu/images/worklog53.png)

### Dịch vụ AWS sử dụng

* AWS Amplify: Triển khai giao diện React.
* Amazon Cognito: Đăng ký và đăng nhập người dùng.
* Amazon API Gateway: Cung cấp REST API.
* AWS Lambda: Xử lý nghiệp vụ.
* Amazon Polly: Chuyển văn bản thành giọng nói.
* Amazon Transcribe: Chuyển giọng nói thành văn bản.
* Amazon S3: Lưu trữ các tệp âm thanh.
* Amazon DynamoDB: Lưu lịch sử chuyển đổi.

### Luồng hoạt động

#### Luồng Text-to-Speech

1. Người dùng đăng nhập bằng Cognito.
2. Frontend gửi yêu cầu tới API Gateway.
3. API Gateway gọi Lambda.
4. Lambda gọi Amazon Polly để tạo giọng nói.
5. Tệp MP3 được lưu vào Amazon S3.
6. Lambda lưu thông tin lịch sử vào DynamoDB.
7. URL tệp âm thanh được trả về frontend.
8. Người dùng có thể nghe hoặc tải xuống.

#### Luồng Speech-to-Text

1. Người dùng đăng nhập bằng Cognito.
2. Frontend tải tệp âm thanh lên thông qua API Gateway.
3. API Gateway chuyển yêu cầu đến Lambda.
4. Lambda lưu tệp âm thanh vào Amazon S3.
5. Lambda gọi Amazon Transcribe để nhận dạng nội dung.
6. Amazon Transcribe trả về văn bản.
7. Lambda lưu lịch sử chuyển đổi vào DynamoDB.
8. Kết quả văn bản được trả về frontend để hiển thị cho người dùng.

---

## 4. Timeline

### Giai đoạn 1

* Nghiên cứu Amazon Polly.
* Nghiên cứu Amazon Transcribe.
* Tìm hiểu kiến trúc Serverless.
* Thiết kế hệ thống.

### Giai đoạn 2

* Xây dựng Backend.
* Tạo Lambda.
* Tích hợp API Gateway.
* Kết nối Amazon Polly.
* Kết nối Amazon Transcribe.
* Lưu tệp âm thanh vào Amazon S3.

### Giai đoạn 3

* Xây dựng giao diện React.
* Tích hợp Cognito.
* Kết nối Backend.
* Hoàn thiện chức năng Text-to-Speech và Speech-to-Text.

### Giai đoạn 4

* Kiểm thử.
* Hoàn thiện giao diện.
* Triển khai lên AWS Amplify.

---

## 5. Ngân sách

Ứng dụng sử dụng các dịch vụ thuộc AWS Free Tier trong quá trình phát triển.

Ước tính chi phí khi triển khai ở quy mô nhỏ:

| Dịch vụ           | Chi phí/tháng |
| ----------------- | ------------- |
| Amazon Polly      | ~0.20 USD     |
| Amazon Transcribe | ~0.10 USD     |
| AWS Lambda        | ~0.00 USD     |
| API Gateway       | ~0.01 USD     |
| Amazon S3         | ~0.05 USD     |
| DynamoDB          | ~0.02 USD     |
| AWS Amplify       | ~0.10 USD     |
| Amazon Cognito    | ~0.00 USD     |

**Tổng chi phí ước tính:** khoảng **0.48 USD/tháng**.

---

## 6. Rủi ro

### Các rủi ro

* Người dùng nhập văn bản vượt giới hạn.
* Người dùng tải lên tệp âm thanh không đúng định dạng.
* Giới hạn Free Tier của Amazon Polly và Amazon Transcribe.
* Tệp âm thanh lưu trữ quá nhiều làm tăng chi phí Amazon S3.
* Lỗi xác thực người dùng.

### Giải pháp

* Giới hạn số ký tự cho mỗi lần chuyển đổi văn bản.
* Chỉ chấp nhận các định dạng âm thanh được hỗ trợ.
* Thiết lập IAM theo nguyên tắc Least Privilege.
* Xóa hoặc lưu trữ định kỳ các tệp cũ.
* Sử dụng Cognito JWT để xác thực mọi API.

---

## 7. Kết quả kỳ vọng

Sau khi hoàn thành, hệ thống sẽ:

* Cho phép người dùng đăng ký và đăng nhập.
* Chuyển đổi văn bản thành giọng nói bằng Amazon Polly.
* Chuyển đổi giọng nói thành văn bản bằng Amazon Transcribe.
* Phát và tải xuống các tệp MP3.
* Hiển thị kết quả nhận dạng giọng nói.
* Lưu lịch sử chuyển đổi của cả hai chức năng.
* Triển khai hoàn toàn trên nền tảng AWS Serverless.
* Có thể mở rộng thêm nhiều ngôn ngữ, nhiều giọng đọc và tích hợp các dịch vụ AI khác trong tương lai.

