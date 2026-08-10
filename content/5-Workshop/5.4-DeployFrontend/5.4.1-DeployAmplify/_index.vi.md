---
title: "Triển khai Frontend bằng AWS Amplify Hosting"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

Trong phần này, frontend React của Polly Voice được kết nối với GitHub và triển
khai bằng AWS Amplify Hosting. Amplify tự động cài đặt dependencies, build source
Vite, publish thư mục `dist` và cung cấp HTTPS domain cho ứng dụng.

1. Truy cập [AWS Amplify Console](https://console.aws.amazon.com/amplify/). Kiểm tra region đang được chọn là **Europe (Stockholm) – `eu-north-1`**.

![Amplify Hosting](/images/5-Workshop/5.4-DeployFrontend/amplify-hosting.png)

2. Chọn **Deploy an app** để tạo một Amplify Hosting application.

![Deploy an app](/images/5-Workshop/5.4-DeployFrontend/deploy-an-app.png)

3. Chọn **GitHub** làm source code provider và nhấn **Next**. Một cửa sổ đăng
nhập và cấp quyền có thể xuất hiện. Sau khi đăng nhập GitHub, chọn
**Authorize AWS Amplify** để cho phép Amplify truy cập repository.

![GitHub](/images/5-Workshop/5.4-DeployFrontend/selectGithub.png)

4. Chọn repository chứa source Polly Voice. Trong mục **Select branch**, chọn
branch muốn sử dụng làm production branch, ví dụ `main`, sau đó nhấn **Next**.

![Add repository](/images/5-Workshop/5.4-DeployFrontend/addRepo.png)

5. Tại trang **App settings**, nhập `polly-voice` trong mục **App name**.

![App name](/images/5-Workshop/5.4-DeployFrontend/appName.png)

Do repository chứa cả frontend và backend, chọn **Edit YAML file** và thay build
configuration bằng nội dung sau:

```yaml
version: 1
applications:
  - appRoot: frontend
    frontend:
      phases:
        preBuild:
          commands:
            - nvm use 22
            - npm ci
        build:
          commands:
            - npm run build
      artifacts:
        baseDirectory: dist
        files:
          - "**/*"
      cache:
        paths:
          - node_modules/**/*
```

Trong đó:

- `appRoot: frontend` xác định thư mục chứa ứng dụng React.
- `nvm use 22` chọn Node.js 22 trong môi trường build.
- `npm ci` cài dependencies theo `package-lock.json`.
- `npm run build` kiểm tra TypeScript và tạo production bundle bằng Vite.
- `baseDirectory: dist` xác định thư mục artifact cần publish.

6. Mở **Advanced settings**, tìm phần **Environment variables** và thêm các biến:

| Tên biến | Giá trị | Ý nghĩa |
|---|---|---|
| `VITE_API_BASE_URL` | `https://<API_ID>.execute-api.eu-north-1.amazonaws.com/api/v1` | Địa chỉ API Gateway HTTP API để frontend gọi TTS, STT và History. Giá trị được lấy từ `ApiUrl` trong CloudFormation Outputs và bổ sung `/api/v1`. |
| `VITE_AWS_ENABLED` | `false` | Tạm thời chưa bật Cognito trong bước này. Biến sẽ được đổi thành `true` sau khi hoàn thành phần 5.4.2. |
| `VITE_AWS_REGION` | `eu-north-1` | Region Europe (Stockholm), nơi các tài nguyên của Polly Voice được triển khai. |

Ví dụ:

```text
VITE_API_BASE_URL=https://abc123.execute-api.eu-north-1.amazonaws.com/api/v1
VITE_AWS_ENABLED=false
VITE_AWS_REGION=eu-north-1
```

`ApiUrl` được lấy tại:

```text
AWS CloudFormation
→ Stacks
→ polly-voice-api
→ Outputs
→ ApiUrl
```


Sau khi hoàn tất, nhấn **Next**.

7. Kiểm tra repository, branch, app root, build command, output directory và các environment variables. Sau đó chọn **Save and deploy** để bắt đầu triển khai.

![Review information](/images/5-Workshop/5.4-DeployFrontend/checkinfo.png)

8. Amplify thực hiện lần lượt các giai đoạn **Provision**, **Build**, **Deploy** và **Verify**. Quá trình này có thể mất vài phút.

Deployment thành công khi branch hiển thị trạng thái **Deployed** màu xanh và Amplify cung cấp một HTTPS URL có dạng:

```text
https://<branch>.<app-id>.amplifyapp.com
```

![Deployed](/images/5-Workshop/5.4-DeployFrontend/result.png)

9. Mở URL do Amplify cung cấp và xác nhận giao diện Polly Voice được hiển thị.
Ghi lại domain này để cập nhật CORS cho backend và cấu hình Cognito trong phần
tiếp theo.

![Polly Voice website](/images/5-Workshop/5.4-DeployFrontend/website.png)
