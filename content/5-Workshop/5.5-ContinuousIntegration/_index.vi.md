---
title: "Thiết lập quy trình CI/CD"
date: 2026-07-31
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

# Thiết lập quy trình CI/CD

## 1. Tổng quan quy trình CI/CD

Quy trình CI/CD của Polly Voice được chia thành hai phần có vai trò khác nhau.
Continuous Integration (CI) kiểm tra chất lượng mã nguồn thông qua unit test,
typecheck, lint, build và xác thực AWS SAM template. Continuous Deployment (CD)
tiếp nhận phiên bản đã vượt qua CI để triển khai backend lên AWS.

Luồng tổng thể được thiết kế như sau:

```text
Developer
    ↓
GitHub
    ↓
Continuous Integration
    ↓
Test, typecheck, lint, build và SAM validation
    ↓
Pull Request được hợp nhất vào main
    ↓
Continuous Deployment
    ↓
GitHub OIDC → AWS credential tạm thời
    ↓
SAM deploy → CloudFormation cập nhật backend
    ↓
Kiểm tra endpoint /health
```

GitHub Actions đóng vai trò control plane điều phối các bước CI/CD, không tham
gia vào luồng xử lý request khi ứng dụng đang chạy. Quy trình đã hoàn thành từ
khâu kiểm tra mã nguồn, triển khai backend đến bảo vệ frontend, giám sát và
cảnh báo vận hành.

## 2. Điều kiện tiên quyết

Các điều kiện dùng chung cho cả CI và CD gồm:

| Thành phần | Cấu hình |
|---|---|
| AWS account | Tài khoản dùng để quản trị và triển khai backend |
| GitHub repository | `super-chickens-aws/polly-voice` |
| Nhánh triển khai | `main` |
| Backend runtime | Node.js 22 |
| Công cụ quản lý dependency | npm |
| Công cụ quản lý mã nguồn | Git |
| Công cụ AWS cục bộ | AWS CLI và AWS SAM CLI |
| Quản lý hạ tầng | AWS SAM và AWS CloudFormation |
| Nền tảng tự động hóa | GitHub Actions |
| Region backend | `eu-north-1` |
| AWS profile cục bộ | `polly-dev` |
| Phân quyền | Các quyền IAM cần thiết cho từng bước triển khai |
| Tài nguyên ứng dụng | Các tài nguyên hiện có của Polly Voice |

AWS profile `polly-dev` chỉ phục vụ thao tác quản trị trên máy phát triển.
Access key cục bộ không được đưa vào GitHub Secrets. Trong quy trình triển khai
tự động, GitHub Actions xác thực bằng OpenID Connect (OIDC) và sử dụng thông tin
xác thực tạm thời thay cho access key dài hạn.

## 3. Triển khai Continuous Integration

### Step 1 — Kiểm tra hiện trạng repository

Trước khi xây dựng workflow, nhóm rà soát tài liệu kiến trúc, mã nguồn Lambda,
SAM template, các tệp `package.json`, lockfile và cấu hình TypeScript. Mục tiêu
là bảo đảm pipeline mới bám đúng kiến trúc hiện tại của repository.

Kết quả kiểm tra trên máy phát triển cho thấy repository đang ở nhánh
`lab/4.3-ci`. Máy sử dụng Node.js `v24.15.0`, npm `11.12.1` và AWS SAM CLI
`1.164.0`. SAM template xác nhận backend dùng runtime `nodejs22.x`, API thuộc
loại `AWS::Serverless::HttpApi` và Lambda handler là `lambda.handler`.

![Kết quả kiểm tra repository Polly Voice](/images/5-Workshop/5.5-ContinuousIntegration/00-repository-audit.png)

**Hình 5.5.1.** Kết quả kiểm tra nhánh Git, phiên bản công cụ và cấu hình AWS SAM của repository Polly Voice.

### Step 2 — Xây dựng workflow Repository CI

Workflow `Repository CI` được tạo trong repository ứng dụng tại:

```text
.github/workflows/ci.yml
```

Workflow chỉ cần đọc mã nguồn nên sử dụng quyền tối thiểu:

```yaml
permissions:
  contents: read
```

GitHub Actions thiết lập Node.js 22 và cài dependency bằng `npm ci` để bảo đảm
dependency được lấy đúng theo lockfile. Các bước kiểm tra backend gồm:

```bash
npm ci
npm test
npm run typecheck
npm run build
```

Frontend được kiểm tra bằng các lệnh:

```bash
npm ci
npm run lint
npm run build
```

Phần hạ tầng được xác thực và build bằng AWS SAM:

```bash
sam validate --lint --template-file backend/template.yaml
sam build \
  --template-file backend/template.yaml \
  --build-dir backend/.aws-sam/build-ci
```

Trước khi cài đặt dependency, workflow còn kiểm tra UTF-8 BOM và cú pháp của
các tệp YAML. Những tệp cấu hình tùy chọn, chẳng hạn cấu hình AWS Amplify, chỉ
được kiểm tra khi thực sự tồn tại trong repository.

### Step 3 — Kiểm thử CI trên môi trường cục bộ

Trước khi đưa workflow lên GitHub, nhóm chạy các bước tương ứng trên máy phát
triển để xác nhận backend, frontend và SAM template có thể vượt qua quy trình
kiểm tra.

```powershell
# Backend
Set-Location backend
npm.cmd ci
npm.cmd test
npm.cmd run typecheck
npm.cmd run build

# Frontend
Set-Location ../frontend
npm.cmd ci
npm.cmd run lint
npm.cmd run build

# AWS SAM
Set-Location ..
$env:PATH = "$(Resolve-Path backend/node_modules/.bin);$env:PATH"

sam validate --lint `
  --template-file backend/template.yaml

sam build `
  --template-file backend/template.yaml `
  --build-dir backend/.aws-sam/build-ci

# Kiểm tra lỗi khoảng trắng và định dạng trong thay đổi Git
git diff --check
```

Kết quả kiểm thử cục bộ được ghi nhận như sau:

| Mã kiểm thử | Nội dung | Kết quả mong đợi | Kết quả thực tế | Trạng thái |
|---|---|---|---|---|
| CI-LOCAL-01 | Chạy unit test backend | Tất cả unit test đạt | 1 tệp test, 1 test đạt | Đạt |
| CI-LOCAL-02 | Typecheck backend | Không có lỗi TypeScript | Exit code 0 | Đạt |
| CI-LOCAL-03 | Build backend | Biên dịch TypeScript thành công | Exit code 0 | Đạt |
| CI-LOCAL-04 | Lint frontend | Không có lỗi Oxlint | Exit code 0 | Đạt |
| CI-LOCAL-05 | Build frontend | Tạo được thư mục `dist` | Vite build hoàn tất | Đạt |
| SAM-LOCAL-01 | Validate SAM template | Template hợp lệ | SAM xác nhận template hợp lệ | Đạt |
| SAM-LOCAL-02 | Build SAM application | Build hoàn tất | Hiển thị `Build Succeeded` | Đạt |

### Step 4 — Kiểm tra CI bằng GitHub Actions

Sau khi workflow được đẩy lên GitHub, hai lần kích hoạt tương ứng với sự kiện
`push` và `pull_request` đều hoàn thành thành công. Kết quả kiểm tra được tổng
hợp trong bảng sau:

| Mã kiểm thử | Nội dung | Kết quả mong đợi | Kết quả thực tế | Trạng thái |
|---|---|---|---|---|
| CI-GITHUB-01 | Kiểm tra workflow khi push | Workflow hoàn tất thành công | Check `push` thành công | Đạt |
| CI-GITHUB-02 | Kiểm tra workflow trên Pull Request | Workflow hoàn tất thành công | Check `pull_request` thành công | Đạt |

GitHub ghi nhận `All checks have passed` với hai check thành công. Sau khi hai
check GitHub Actions hoàn thành, Pull Request số 3 được hợp nhất vào nhánh
`main`.

![Kết quả kiểm tra Pull Request số 3](/images/5-Workshop/5.5-ContinuousIntegration/01-pr-checks.png)

**Hình 5.5.2.** Hai check GitHub Actions hoàn thành và Pull Request số 3 không có xung đột với nhánh `main` trước khi hợp nhất.

### Step 5 — Hợp nhất workflow vào branch main

Pull Request số 3 được hợp nhất với merge commit `3f638a3`. Sau khi cập nhật
repository cục bộ, nhánh `main` đồng bộ với `origin/main` và tệp
`.github/workflows/ci.yml` tồn tại trên nhánh `main`. Như vậy, workflow CI đã
trở thành một phần của nhánh chính và có thể tiếp tục làm nền tảng cho quá
trình xây dựng CD.

![Pull Request số 3 được hợp nhất vào main](/images/5-Workshop/5.5-ContinuousIntegration/02-pr-merged.png)

**Hình 5.5.3.** Pull Request số 3 được hợp nhất vào nhánh `main` sau khi các kiểm tra GitHub Actions hoàn thành thành công.

## 4. Triển khai Continuous Deployment

### Step 6 — Cấu hình môi trường triển khai GitHub

Nhóm đã tạo GitHub Environment `dev` để tách cấu hình triển khai khỏi mã nguồn
workflow. Environment này chỉ cho phép nhánh `main` thực hiện deployment, nhờ
đó phiên bản backend được triển khai luôn xuất phát từ nhánh chính đã vượt qua
quy trình CI.

Các environment variables phục vụ deployment gồm:

| Biến | Vai trò |
|---|---|
| `AWS_REGION` | Xác định Region triển khai `eu-north-1` |
| `AWS_DEPLOY_ROLE_ARN` | Xác định GitHub deployment role |
| `CFN_EXECUTION_ROLE_ARN` | Xác định CloudFormation execution role |
| `SAM_ARTIFACTS_BUCKET` | Xác định bucket lưu SAM deployment artifacts |
| `CFN_STACK_NAME` | Xác định stack `polly-voice-api` |
| `MEDIA_BUCKET_NAME` | Truyền tên S3 media bucket hiện hữu |
| `HISTORY_TABLE_NAME` | Truyền tên DynamoDB history table hiện hữu |
| `COGNITO_USER_POOL_ID` | Truyền định danh Cognito User Pool |
| `COGNITO_CLIENT_ID` | Truyền định danh Cognito application client |
| `FRONTEND_ORIGIN` | Xác định origin được backend cho phép |

Việc quản lý các giá trị theo GitHub Environment giúp workflow có thể tái sử
dụng mà không hardcode cấu hình của môi trường `dev`.

![Cấu hình GitHub Environment dev](/images/5-Workshop/5.5-ContinuousIntegration/07-github-environment-dev.png)

**Hình 5.5.4.** GitHub Environment `dev` được giới hạn cho nhánh `main` và chứa các biến phục vụ quá trình deployment.

### Step 7 — Thiết lập GitHub OIDC và IAM

GitHub Actions đã được cấu hình xác thực với AWS bằng OpenID Connect, sử dụng
audience `sts.amazonaws.com`. Mỗi lần workflow chạy, GitHub nhận thông tin xác
thực AWS tạm thời và không phải lưu access key dài hạn trong GitHub Secrets.

Hai IAM role được tách theo trách nhiệm:

| IAM role | Trách nhiệm |
|---|---|
| `polly-voice-github-deploy-dev` | Điều khiển hoạt động deployment từ GitHub Actions |
| `polly-voice-cfn-execution-dev` | Thực hiện các thay đổi tài nguyên do CloudFormation stack quản lý |

GitHub deployment role được workflow sử dụng để khởi tạo quá trình triển khai,
trong khi CloudFormation execution role thực thi thay đổi hạ tầng. Cách phân
tách này giới hạn quyền theo đúng trách nhiệm và áp dụng nguyên tắc quyền tối
thiểu.

### Step 8 — Chuẩn bị AWS SAM deployment

Một S3 artifact bucket tại Region `eu-north-1` được sử dụng để lưu các gói triển
khai do AWS SAM tạo ra. Bucket đã bật Block Public Access và mã hóa mặc định,
giúp deployment artifacts không được công khai ngoài ý muốn.

SAM template nhận tên DynamoDB table và S3 media bucket hiện hữu thông qua
parameters. Stack không tạo lại hai tài nguyên dữ liệu này; Lambda nhận tên
resource qua environment variables và SAM policies cấp quyền dựa trên các tên
được truyền vào.

Trước khi triển khai, template đã vượt qua toàn bộ bước kiểm tra:

| Kiểm tra | Kết quả |
|---|---|
| Backend unit test | Đạt |
| TypeScript typecheck | Đạt |
| Backend build | Đạt |
| `sam validate` | Template hợp lệ |
| `sam build` | Build thành công |

### Step 9 — Xây dựng workflow Continuous Deployment

Workflow Continuous Deployment được đặt tại:

```text
.github/workflows/backend-deploy.yml
```

Workflow triển khai vào GitHub Environment `dev` khi thay đổi backend được hợp
nhất vào nhánh `main`. Ngoài cơ chế tự động này, workflow còn hỗ trợ
`workflow_dispatch` để khởi chạy thủ công khi cần.

Luồng deployment thành công gồm:

1. Checkout repository.
2. Thiết lập Node.js 22.
3. Cài đặt AWS SAM CLI.
4. Cài dependency backend bằng `npm ci`.
5. Chạy backend unit test.
6. Chạy TypeScript typecheck.
7. Build backend.
8. Validate SAM template.
9. Build SAM application.
10. Xác thực AWS thông qua GitHub OIDC.
11. Thực hiện `sam deploy`.
12. Đọc output `ApiUrl` từ CloudFormation.
13. Gọi endpoint `/health`.
14. Ghi deployment summary.

GitHub OIDC cung cấp thông tin xác thực tạm thời cho workflow. CloudFormation
execution role được truyền vào quá trình triển khai để CloudFormation thực
hiện các thay đổi tài nguyên thuộc stack.

### Step 10 — Triển khai backend thành công

Workflow đã triển khai backend với kết quả:

| Thuộc tính | Kết quả |
|---|---|
| Environment | `dev` |
| Region | `eu-north-1` |
| Stack | `polly-voice-api` |
| CloudFormation stack status | `CREATE_COMPLETE` |
| Health check | `Passed` |

GitHub Actions nhận thông tin xác thực tạm thời qua OIDC, sau đó AWS SAM đóng
gói và triển khai backend. AWS CloudFormation quản lý tài nguyên ứng dụng trong
stack `polly-voice-api`. Khi deployment hoàn tất, workflow đọc API URL từ stack
output và gọi endpoint `/health` để xác nhận backend hoạt động.

![GitHub Actions triển khai backend thành công](/images/5-Workshop/5.5-ContinuousIntegration/09-backend-cd-success.png)

**Hình 5.5.5.** Workflow CD hoàn tất deployment backend vào môi trường `dev` và xác nhận health check ở trạng thái `Passed`.

<div style="overflow: hidden;">
  <img src="/images/5-Workshop/5.5-ContinuousIntegration/10-cloudformation-stack.png" alt="CloudFormation stack được tạo thành công" style="display: block; width: 100%; clip-path: inset(75px 0 0 0); margin-top: -75px;">
</div>

**Hình 5.5.6.** Stack `polly-voice-api` đạt trạng thái `CREATE_COMPLETE` tại Region `eu-north-1`.

![Endpoint health check phản hồi thành công](/images/5-Workshop/5.5-ContinuousIntegration/11-health-check.png)

**Hình 5.5.7.** Endpoint `/health` phản hồi trạng thái `ok`, xác nhận dịch vụ `polly-voice-backend` hoạt động tại Region `eu-north-1`.

### Step 11 — Bảo vệ frontend bằng AWS WAF

AWS WAF đã được liên kết với ứng dụng AWS Amplify Hosting và firewall đã
chuyển sang trạng thái `Enabled`. Web ACL hoạt động như một lớp bảo vệ phía
trước frontend Amplify, giúp kiểm soát lưu lượng truy cập vào ứng dụng. WAF
không được mô tả là gắn trực tiếp với API Gateway HTTP API.

<div style="overflow: hidden;">
  <img src="/images/5-Workshop/5.5-ContinuousIntegration/12-amplify-waf-enabled.png" alt="AWS WAF được bật cho Amplify Hosting" style="display: block; width: 100%; clip-path: inset(75px 0 0 0); margin-top: -75px;">
</div>

**Hình 5.5.8.** AWS WAF đã được liên kết với ứng dụng Amplify Hosting và firewall ở trạng thái `Enabled`.

### Step 12 — Tạo CloudWatch Dashboard

Nhóm đã tạo CloudWatch Dashboard `polly-voice-dev` để theo dõi backend tại
Region `eu-north-1`. Dashboard tổng hợp các metric:

- Lambda Invocations.
- Lambda Errors.
- Lambda Average Duration.
- Lambda Throttles.
- HTTP API Count.
- HTTP API 4xx.
- HTTP API 5xx.
- HTTP API Latency.
- HTTP API IntegrationLatency.

Dashboard cung cấp góc nhìn tập trung về traffic, lỗi và hiệu năng của Lambda
cùng API Gateway HTTP API sau deployment.

<div style="overflow: hidden;">
  <img src="/images/5-Workshop/5.5-ContinuousIntegration/13-cloudwatch-dashboard.png" alt="CloudWatch dashboard của môi trường development" style="display: block; width: 100%; clip-path: inset(75px 0 0 0); margin-top: -75px;">
</div>

**Hình 5.5.9.** Dashboard `polly-voice-dev` tổng hợp các metric vận hành của AWS Lambda và API Gateway HTTP API tại Region `eu-north-1`.

### Step 13 — Cấu hình CloudWatch Alarm và Amazon SNS

Amazon SNS topic `polly-voice-alerts-dev` đã được cấu hình với email
subscription được xác nhận. Địa chỉ email không được đưa vào báo cáo. Hai
CloudWatch Alarm sử dụng topic này để gửi thông báo khi phát hiện lỗi; OK action
cũng được liên kết với SNS để thông báo khi hệ thống phục hồi.

| Alarm | Metric giám sát | Trạng thái trong ảnh |
|---|---|---|
| `polly-voice-dev-lambda-errors` | `Errors` của AWS Lambda | `INSUFFICIENT_DATA`, action đã bật |
| `polly-voice-dev-api-5xx` | `5xx` của API Gateway | `INSUFFICIENT_DATA`, action đã bật |

Trạng thái `INSUFFICIENT_DATA` ngay sau khi tạo cho biết chưa có đủ dữ liệu để
đánh giá alarm, không phải lỗi triển khai. Cấu hình không yêu cầu cố tình gây
lỗi hệ thống để kích hoạt cảnh báo.

<div style="overflow: hidden;">
  <img src="/images/5-Workshop/5.5-ContinuousIntegration/14-cloudwatch-alarms.png" alt="CloudWatch alarms giám sát lỗi backend" style="display: block; width: 100%; clip-path: inset(75px 0 0 0); margin-top: -75px;">
</div>

**Hình 5.5.10.** Hai alarm giám sát Lambda Errors và API Gateway 5xx đã được cấu hình, bật action và liên kết với Amazon SNS topic `polly-voice-alerts-dev`.

## 5. Kết quả đạt được

Hệ thống Polly Voice đã hoàn thành quy trình Continuous Integration và
Continuous Deployment. CI kiểm tra chất lượng mã nguồn trước khi Pull Request
được hợp nhất; CD tự động triển khai backend sau khi thay đổi được đưa vào
nhánh `main`.

Các kết quả chính gồm:

- GitHub OIDC đã loại bỏ nhu cầu lưu access key AWS dài hạn trên GitHub.
- AWS SAM và CloudFormation đã cung cấp quy trình triển khai hạ tầng có thể lặp
  lại.
- Stack `polly-voice-api` đã đạt trạng thái `CREATE_COMPLETE`.
- Backend health check đã hoàn thành thành công.
- AWS WAF đã bảo vệ frontend được triển khai bằng Amplify Hosting.
- CloudWatch Dashboard đã cung cấp góc nhìn tổng hợp về hoạt động backend.
- CloudWatch Alarms và Amazon SNS đã hỗ trợ giám sát, cảnh báo lỗi và thông báo
  khi hệ thống phục hồi.

Kiến trúc CI/CD và giám sát sau khi hoàn thành được mô tả như sau:

```text
Developer
    ↓
GitHub
    ↓
Repository CI
    ↓
Merge vào main
    ↓
Backend CD
    ↓
GitHub OIDC
    ↓
AWS SAM
    ↓
AWS CloudFormation
    ↓
API Gateway HTTP API và Lambda
    ↓
CloudWatch Dashboard, Alarms và SNS
```

AWS WAF hoạt động như lớp bảo vệ riêng cho frontend trên AWS Amplify Hosting.
