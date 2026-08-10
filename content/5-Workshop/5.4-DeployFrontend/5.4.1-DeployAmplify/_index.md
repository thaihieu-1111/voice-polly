---
title: "Deploy the Frontend with AWS Amplify Hosting"
date: 2026-07-30
weight: 1
chapter: false
pre: " <b> 5.5.1. </b> "
---

# Deploy the Frontend with AWS Amplify Hosting

In this section, the Polly Voice React frontend is connected to GitHub and
deployed with AWS Amplify Hosting. Amplify automatically installs the
dependencies, builds the Vite source, publishes the `dist` directory, and
provides an HTTPS domain for the application.

1. Open the [AWS Amplify Console](https://console.aws.amazon.com/amplify/).
Verify that the selected region is **Europe (Stockholm) – `eu-north-1`**.

![Amplify Hosting](/images/5-Workshop/5.4-DeployFrontend/amplify-hosting.png)

2. Select **Deploy an app** to create an Amplify Hosting application.

![Deploy an app](/images/5-Workshop/5.4-DeployFrontend/deploy-an-app.png)

3. Select **GitHub** as the source code provider and choose **Next**. A sign-in
and authorization window may appear. After signing in to GitHub, select
**Authorize AWS Amplify** to allow Amplify to access the repository.

![GitHub](/images/5-Workshop/5.4-DeployFrontend/selectGithub.png)

4. Select the repository containing the Polly Voice source code. Under
**Select branch**, choose the branch to use as the production branch, such as
`main`, and then select **Next**.

![Add repository](/images/5-Workshop/5.4-DeployFrontend/addRepo.png)

5. On the **App settings** page, enter `polly-voice` for **App name**.

![App name](/images/5-Workshop/5.4-DeployFrontend/appName.png)

Because the repository contains both the frontend and backend, select
**Edit YAML file** and replace the build configuration with:

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

The configuration has the following purposes:

- `appRoot: frontend` identifies the directory containing the React application.
- `nvm use 22` selects Node.js 22 in the build environment.
- `npm ci` installs dependencies according to `package-lock.json`.
- `npm run build` checks TypeScript and creates the production Vite bundle.
- `baseDirectory: dist` identifies the artifacts to publish.

{{% notice warning %}}
When `appRoot: frontend` is configured, the build output directory is `dist`,
not `frontend/dist`. An incorrect value can cause Amplify to display its Welcome
page because it cannot locate `index.html`.
{{% /notice %}}

6. Expand **Advanced settings**, find **Environment variables**, and add:

| Variable | Value | Description |
|---|---|---|
| `VITE_API_BASE_URL` | `https://<API_ID>.execute-api.eu-north-1.amazonaws.com/api/v1` | The API Gateway HTTP API address used by the frontend for TTS, STT, and History. Obtain `ApiUrl` from the CloudFormation Outputs and append `/api/v1`. |
| `VITE_AWS_ENABLED` | `false` | Cognito is temporarily disabled in this step. This value will be changed to `true` after section 5.4.2 is completed. |
| `VITE_AWS_REGION` | `eu-north-1` | The Europe (Stockholm) region where the Polly Voice resources are deployed. |

Example:

```text
VITE_API_BASE_URL=https://abc123.execute-api.eu-north-1.amazonaws.com/api/v1
VITE_AWS_ENABLED=false
VITE_AWS_REGION=eu-north-1
```

Retrieve `ApiUrl` from:

```text
AWS CloudFormation
→ Stacks
→ polly-voice-api
→ Outputs
→ ApiUrl
```

{{% notice info %}}
Variables prefixed with `VITE_` are embedded in the JavaScript bundle during the
build. Redeploy the frontend after changing an environment variable.
{{% /notice %}}

{{% notice warning %}}
Do not store an AWS access key, secret access key, password, JWT, or presigned
URL in Amplify environment variables.
{{% /notice %}}

When the configuration is complete, select **Next**.

7. Review the repository, branch, app root, build command, output directory, and
environment variables. Then select **Save and deploy** to begin the deployment.

![Review information](/images/5-Workshop/5.4-DeployFrontend/checkinfo.png)

8. Amplify runs the **Provision**, **Build**, **Deploy**, and **Verify** stages.
This process can take several minutes.

The deployment is successful when the branch shows a green **Deployed** status
and Amplify provides an HTTPS URL in the following format:

```text
https://<branch>.<app-id>.amplifyapp.com
```

![Deployed](/images/5-Workshop/5.4-DeployFrontend/result.png)

9. Open the URL provided by Amplify and verify that the Polly Voice interface is
displayed. Record this domain for the backend CORS update and the Cognito
configuration in the next section.

![Polly Voice website](/images/5-Workshop/5.4-DeployFrontend/website.png)

{{% notice info %}}
At this point, the frontend is hosted on Amplify and can call API Gateway.
Cognito sign-up and sign-in will be configured in section 5.4.2.
{{% /notice %}}
