---
title: "Kết nối Frontend với Amazon Cognito"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

Trong phần này, Amazon Cognito User Pool được cấu hình để quản lý tài khoản của
Polly Voice. Frontend React sử dụng Cognito để đăng ký, gửi mã xác nhận email,
đăng nhập, lấy access token và gửi token đó tới backend.

1. Truy cập [Amazon Cognito Console](https://console.aws.amazon.com/cognito/).
Kiểm tra region đang được chọn là **Europe (Stockholm) – `eu-north-1`**, sau đó
chọn **User Pools** ở thanh bên trái.

![Open Amazon Cognito](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/01-open-cognito.png)
![Open Amazon Cognito](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/01-open-cognito-2.png)

2. Chọn **Create user pool** để tạo một User Pool mới.

![Create user pool](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/01-create-user-pool.png)

3. Trong phần **Define your application**, chọn: **Single-page app (SPA)**. Loại SPA phù hợp với React frontend và tạo App Client. Sau đó nhập tên ứng dụng trong **Name your application**. Ví dụ: `polly-voice`.

![Define the Cognito application](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/02-define-application.png)

4. Trong phần **Options for sign-in identifiers**, chỉ giữ **E-mail**:
Polly Voice sử dụng email làm thông tin đăng ký và đăng nhập. Việc bỏ username
và phone number giúp giao diện không phải xử lý thêm định danh hoặc xác minh số
điện thoại. Trong phần **Required attributes for sign-up**, chọn thuộc tính `name` nếu
AWS Console cho phép lựa chọn thuộc tính bổ sung. Email đã được sử dụng làm
sign-in identifier.

![Select email sign-in](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/03-email-sign-in.png)


Frontend gửi hai thuộc tính sau khi đăng ký:

```text
email
name
```

5. Trong phần **Add a return URL**, nhập Amplify domain đã nhận tại phần 5.4.1:

```text
https://<branch>.<app-id>.amplifyapp.com
```
Sau đó chọn **Create user directory** để tạo Cognito User Pool.

![Configure user attributes](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/04-user-attributes1.png)


## Lấy thông tin kết nối Cognito

1. Sau khi tạo thành công, mở:

```text
Amazon Cognito
→ User pools
→ User Pool vừa tạo
→ Overview
```

Tìm và ghi lại **User pool ID**. Giá trị có dạng:

```text
eu-north-1_xxxxxxxxx
```

Đây là giá trị dùng cho:

```text
VITE_COGNITO_USER_POOL_ID
```

![Cognito User Pool ID](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/06-user-pool-id.png)

7. Trong User Pool, mở:

```text
Applications
→ App clients
→ polly-voice
```

Tìm và ghi lại **Client ID**. Giá trị này được dùng cho:

```text
VITE_COGNITO_CLIENT_ID
```

![Cognito App Client ID](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/07-app-client-id.png)

10. Mở Amplify application `polly-voice`, chọn:

```text
Hosting
→ Environment variables
```
![Amplify environment variables](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/08-amplify-environment-variables.png)

Chọn **Manage variables** và thêm các biến:

| Tên biến | Giá trị | Ý nghĩa |
|---|---|---|
| `VITE_AWS_ENABLED` | `true` | Bật chế độ sử dụng Cognito trên frontend. |
| `VITE_AWS_REGION` | `eu-north-1` | Region chứa Cognito User Pool. |
| `VITE_COGNITO_USER_POOL_ID` | `eu-north-1_xxxxxxxxx` | ID của User Pool vừa tạo. |
| `VITE_COGNITO_CLIENT_ID` | `<APP_CLIENT_ID>` | ID của SPA App Client `polly-voice`. |

Sau đó chọn **Save** để lưu các biến.


![Amplify Cognito environment variables](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/10-amplify-environment-variables.png)


11. Sau khi lưu environment variables, chọn **Overview** và chọn chính xác **Production branch** đã build trước đó và chọn **Redeploy this version**.

Việc redeploy là bắt buộc vì Vite đưa các biến `VITE_*` vào JavaScript bundle
trong quá trình build. Chỉ lưu biến mà không build lại sẽ khiến website tiếp tục
sử dụng cấu hình cũ.

![Redeploy the Amplify frontend](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/11-redeploy-frontend.png)
![Redeploy the Amplify frontend](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/11-redeploy-frontend1.png)
## Đồng bộ cấu hình Backend

12. Backend phải xác minh JWT từ cùng User Pool và App Client mà frontend đang
sử dụng. Kiểm tra các Lambda environment variables:

```text
AWS_COGNITO_USER_POOL_ID
AWS_COGNITO_CLIENT_ID
AWS_COGNITO_ISSUER_URI
```

Issuer URI có dạng:

```text
https://cognito-idp.eu-north-1.amazonaws.com/<USER_POOL_ID>
```

Nếu User Pool hoặc App Client vừa được tạo khác với cấu hình backend hiện tại,
cập nhật SAM parameters và deploy lại backend. Frontend và backend dùng hai
User Pool khác nhau sẽ làm JWT bị từ chối.

![Lambda Cognito environment variables](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/12-lambda-cognito-configuration.png)


## Kiểm thử đăng ký và đăng nhập

13. Mở Amplify production website và chọn **Sign up**. Nhập:

- Display name.
- Email.
- Password đáp ứng password policy.

Sau khi đăng ký, giao diện chuyển sang form nhập confirmation code. Mã được
Cognito gửi đến email đã đăng ký.

![Register a Polly Voice account](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/13-sign-up-form.png)

14. Nhập confirmation code và chọn xác nhận. Nếu mã hết hạn hoặc không nhận được
email, sử dụng chức năng **Resend confirmation code**.

Giao diện nhập mã được hiển thị trực tiếp trong ứng dụng, không phụ thuộc popup
của trình duyệt.

![Confirm the email address](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/14-confirm-email.png)

15. Sau khi xác nhận, đăng nhập bằng email và password. Đăng nhập thành công khi:

- Header hiển thị tên user.
- Nút Sign in chuyển thành Sign out.
- Profile hiển thị Cognito Subject ID.
- TTS cho phép giới hạn dành cho user.
- History có thể được truy cập.

![Cognito sign-in succeeded](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/15-sign-in-success.png)

16. Mở Cognito Console, chọn:

```text
User management
→ Users
```

Kiểm tra user vừa tạo có trạng thái:

```text
CONFIRMED
```

![Confirmed Cognito user](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/16-confirmed-user.png)

17. Mở Developer Tools → **Network**, gọi một endpoint được bảo vệ như History
và kiểm tra request có header:

```http
Authorization: Bearer <access-token>
```

Không đưa token đầy đủ vào ảnh báo cáo. Chỉ giữ phần tên header hoặc che phần lớn
giá trị token.

![Authenticated API request](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/17-authenticated-request.png)

## Các lỗi thường gặp

| Lỗi | Nguyên nhân | Cách xử lý |
|---|---|---|
| `User is not confirmed` | User chưa nhập confirmation code | Chuyển sang form Confirm hoặc gửi lại mã |
| `User does not exist` | Email thuộc User Pool khác | Kiểm tra `VITE_COGNITO_USER_POOL_ID` |
| `Incorrect username or password` | Sai email/password hoặc password đã thay đổi | Kiểm tra thông tin và thực hiện reset password nếu cần |
| `Invalid client` | App Client ID không đúng hoặc có client secret | Dùng SPA App Client không có secret |
| API trả HTTP 401 | Frontend và backend dùng khác User Pool/Client ID | Đồng bộ Amplify variables và SAM parameters |
| Website vẫn dùng cấu hình cũ | Chỉ lưu biến nhưng chưa build lại | Redeploy Amplify branch |
| Không nhận được email | Email sai, thư vào Spam hoặc mã chưa được gửi lại | Kiểm tra địa chỉ và dùng Resend code |

## Kết quả

Amazon Cognito đã được kết nối với frontend Polly Voice. Người dùng có thể đăng
ký, xác nhận email, đăng nhập và đăng xuất trên website Amplify. Frontend nhận
access token từ Cognito và gửi token tới API Gateway; backend xác minh token để
phân tách lịch sử TTS/STT theo từng Cognito Subject ID.
