+++
title = '5.3 Kiến trúc giải pháp'
weight = 3

[params]
collapsibleMenu = true
+++

Trước khi triển khai các tài nguyên trên AWS, chúng ta cần hiểu kiến trúc tổng thể của ứng dụng Polly Voice và cách các dịch vụ AWS phối hợp với nhau trong toàn bộ hệ thống.

Ứng dụng được xây dựng theo kiến trúc **Serverless**, trong đó toàn bộ hạ tầng được quản lý bởi AWS. Cách tiếp cận này giúp loại bỏ việc phải cài đặt và quản lý máy chủ, đồng thời cung cấp khả năng tự động mở rộng, tính sẵn sàng cao và tối ưu chi phí vận hành.

---

## 5.3.1 Kiến trúc tổng thể

Ứng dụng Polly Voice gồm ba tầng chính:

* Frontend Layer
* Backend Layer
* Storage Layer

Frontend được triển khai trên **AWS Amplify** và cung cấp giao diện cho người dùng.

Backend bao gồm **Amazon API Gateway** và **AWS Lambda**, chịu trách nhiệm xử lý các yêu cầu từ người dùng và giao tiếp với các dịch vụ AWS khác.

Ứng dụng hỗ trợ hai chức năng chính:

* **Text-to-Speech (TTS):** Chuyển đổi văn bản thành giọng nói bằng **Amazon Polly**.
* **Speech-to-Text (STT):** Chuyển đổi giọng nói thành văn bản bằng **Amazon Transcribe**.

Các tệp âm thanh được tạo hoặc tải lên sẽ được lưu trữ trên **Amazon S3**, trong khi lịch sử chuyển đổi của cả hai chức năng được lưu trong **Amazon DynamoDB**.

Việc xác thực người dùng được thực hiện bởi **Amazon Cognito** trước khi các yêu cầu được phép truy cập vào hệ thống backend.

> **Hình minh họa:** Kiến trúc tổng thể Polly Voice

![Mô tả ảnh](https://super-chickens-aws.github.io/dinhhhiuu/images/worklog53.png)

---

## 5.3.2 Các dịch vụ AWS sử dụng

Ứng dụng Polly Voice sử dụng nhiều dịch vụ được quản lý (Managed Services) của AWS. Mỗi dịch vụ đảm nhiệm một chức năng riêng trong hệ thống.

### AWS Amplify

AWS Amplify được sử dụng để triển khai ứng dụng React.

Dịch vụ này tự động triển khai frontend từ kho mã nguồn GitHub và cung cấp khả năng lưu trữ website thông qua HTTPS cùng với quy trình CI/CD tích hợp sẵn.

---

### Amazon Cognito

Amazon Cognito chịu trách nhiệm xác thực và quản lý người dùng.

Các chức năng chính bao gồm:

* Đăng ký tài khoản.
* Đăng nhập.
* Sinh JWT Token.
* Quản lý phiên đăng nhập của người dùng.

Chỉ những người dùng đã được xác thực mới có thể truy cập các API của hệ thống.

---

### Amazon API Gateway

Amazon API Gateway cung cấp các REST API cho frontend.

Thay vì cho phép frontend gọi trực tiếp đến Lambda, mọi yêu cầu đều được gửi thông qua API Gateway.

API Gateway cũng thực hiện việc xác thực JWT Token do Amazon Cognito cấp trước khi chuyển tiếp yêu cầu đến Lambda.

---

### AWS Lambda

AWS Lambda chứa toàn bộ nghiệp vụ của ứng dụng.

Sau khi nhận yêu cầu từ API Gateway, Lambda sẽ thực hiện các công việc như:

* Kiểm tra dữ liệu đầu vào.
* Gọi Amazon Polly để chuyển đổi văn bản thành giọng nói.
* Gọi Amazon Transcribe để chuyển đổi giọng nói thành văn bản.
* Tải các tệp âm thanh lên Amazon S3.
* Lưu lịch sử chuyển đổi vào Amazon DynamoDB.
* Trả kết quả về frontend.

Do Lambda hoạt động theo mô hình Serverless nên tài nguyên tính toán chỉ được cấp phát khi có yêu cầu.

---

### Amazon Polly

Amazon Polly chuyển đổi văn bản thành giọng nói tự nhiên.

Người dùng có thể lựa chọn nhiều giọng đọc khác nhau trước khi thực hiện chuyển đổi.

Sau khi xử lý xong, Amazon Polly sẽ trả về luồng dữ liệu âm thanh (Audio Stream) để Lambda lưu thành tệp MP3.

---

### Amazon Transcribe

Amazon Transcribe chuyển đổi nội dung giọng nói trong các tệp âm thanh thành văn bản.

Sau khi người dùng tải lên tệp âm thanh, AWS Lambda sẽ gửi yêu cầu đến Amazon Transcribe để thực hiện quá trình nhận dạng giọng nói.

Khi hoàn thành, Amazon Transcribe trả về nội dung văn bản để Lambda tiếp tục xử lý và gửi kết quả về frontend.

---

### Amazon S3

Amazon S3 lưu trữ toàn bộ các tệp âm thanh của ứng dụng.

Đối với chức năng **Text-to-Speech**, Lambda lưu tệp MP3 được tạo bởi Amazon Polly lên S3.

Đối với chức năng **Speech-to-Text**, các tệp âm thanh do người dùng tải lên cũng được lưu trên S3 để Amazon Transcribe xử lý.

Việc lưu trữ tập trung trên Amazon S3 giúp quản lý, phát lại và tải xuống các tệp âm thanh một cách thuận tiện.

---

### Amazon DynamoDB

Amazon DynamoDB lưu trữ thông tin lịch sử của mỗi lần chuyển đổi.

Các thông tin được lưu bao gồm:

* ID người dùng.
* Loại chuyển đổi (Text-to-Speech hoặc Speech-to-Text).
* Nội dung văn bản hoặc kết quả nhận dạng.
* Giọng đọc đã chọn (đối với TTS).
* Đường dẫn tệp âm thanh.
* Thời gian tạo.

Việc lưu riêng dữ liệu lịch sử với tệp âm thanh giúp hệ thống dễ dàng truy vấn và quản lý hơn.

---

## 5.3.3 Luồng xử lý dữ liệu

Ứng dụng hỗ trợ hai luồng xử lý chính: **Text-to-Speech (TTS)** và **Speech-to-Text (STT)**.

### Luồng Text-to-Speech (TTS)

#### Bước 1

Người dùng truy cập ứng dụng Polly Voice được triển khai trên AWS Amplify.

---

#### Bước 2

Người dùng đăng nhập bằng Amazon Cognito.

Sau khi xác thực thành công, Cognito sẽ trả về JWT Token.

---

#### Bước 3

Frontend gửi yêu cầu chuyển văn bản thành giọng nói đến Amazon API Gateway kèm theo JWT Token.

---

#### Bước 4

API Gateway kiểm tra tính hợp lệ của JWT Token.

Nếu xác thực thành công, yêu cầu sẽ được chuyển đến AWS Lambda.

Nếu không hợp lệ, API sẽ từ chối yêu cầu.

---

#### Bước 5

AWS Lambda gửi nội dung văn bản đến Amazon Polly.

Amazon Polly tổng hợp giọng nói và trả về luồng dữ liệu âm thanh.

---

#### Bước 6

Lambda lưu tệp MP3 vừa tạo lên Amazon S3.

---

#### Bước 7

Lambda lưu thông tin lịch sử chuyển đổi vào Amazon DynamoDB.

---

#### Bước 8

Lambda trả về đường dẫn tệp âm thanh cùng thông tin chuyển đổi cho frontend.

---

#### Bước 9

Frontend cho phép người dùng:

* Nghe tệp âm thanh.
* Tải xuống tệp MP3.
* Xem lịch sử các lần chuyển đổi trước đó.

---

### Luồng Speech-to-Text (STT)

#### Bước 1

Người dùng truy cập ứng dụng Polly Voice trên AWS Amplify và đăng nhập bằng Amazon Cognito.

---

#### Bước 2

Frontend gửi yêu cầu Speech-to-Text đến Amazon API Gateway kèm theo JWT Token và tệp âm thanh.

---

#### Bước 3

API Gateway xác thực JWT Token.

Nếu hợp lệ, yêu cầu sẽ được chuyển đến AWS Lambda.

---

#### Bước 4

AWS Lambda lưu tệp âm thanh lên Amazon S3.

---

#### Bước 5

Lambda gửi yêu cầu đến Amazon Transcribe để thực hiện chuyển đổi giọng nói thành văn bản.

---

#### Bước 6

Amazon Transcribe xử lý tệp âm thanh và trả về kết quả nhận dạng.

---

#### Bước 7

Lambda lưu lịch sử chuyển đổi vào Amazon DynamoDB.

---

#### Bước 8

Lambda trả về nội dung văn bản đã nhận dạng cho frontend.

---

#### Bước 9

Frontend hiển thị kết quả nhận dạng để người dùng xem, sao chép hoặc tiếp tục sử dụng.

---

## Ưu điểm của kiến trúc

Kiến trúc được lựa chọn mang lại nhiều lợi ích cho ứng dụng.

### Serverless hoàn toàn

Không cần quản lý máy chủ hay hệ điều hành.

AWS sẽ tự động cấp phát tài nguyên khi có yêu cầu từ người dùng.

---

### Xác thực an toàn

Amazon Cognito xác thực người dùng trước khi các tài nguyên backend được truy cập.

JWT Token giúp ngăn chặn các yêu cầu API trái phép.

---

### Khả năng mở rộng cao

AWS Lambda tự động mở rộng theo số lượng yêu cầu.

Amazon S3, Amazon DynamoDB và Amazon Transcribe đều có khả năng mở rộng tự động mà không cần cấu hình thủ công.

---

### Tối ưu chi phí

Các dịch vụ cốt lõi đều áp dụng mô hình **Pay-as-you-go**, vì vậy hệ thống chỉ phát sinh chi phí khi có người sử dụng.

Kiến trúc này rất phù hợp với các ứng dụng cloud-native có quy mô nhỏ và trung bình.

---

## Tổng kết

Đến thời điểm này, chúng ta đã hiểu được kiến trúc tổng thể của hệ thống, vai trò của từng dịch vụ AWS cũng như hai luồng xử lý chính của ứng dụng: **Text-to-Speech** sử dụng **Amazon Polly** và **Speech-to-Text** sử dụng **Amazon Transcribe**.

Trong phần tiếp theo, chúng ta sẽ bắt đầu triển khai hệ thống backend bằng cách cấu hình Amazon Cognito trên AWS Management Console.

