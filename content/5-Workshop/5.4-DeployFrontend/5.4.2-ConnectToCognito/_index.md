---
title: "Connect the Frontend to Amazon Cognito"
date: 2026-07-30
weight: 2
chapter: false
pre: " <b> 5.5.2. </b> "
---

In this section, an Amazon Cognito User Pool is configured to manage Polly Voice accounts. The React frontend uses Cognito to sign users up, send email confirmation codes, sign users in, obtain access tokens, and send those tokens to the backend.

1. Open the [Amazon Cognito Console](https://console.aws.amazon.com/cognito/). Verify that the selected region is **Europe (Stockholm) – `eu-north-1`**, then select **User Pools** on the left menu.

![Open Amazon Cognito](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/01-open-cognito.png)
![Open Amazon Cognito](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/01-open-cognito-2.png)

2. Select **Create user pool** to create a new User Pool.

![Create user pool](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/01-create-user-pool.png)

3. Under **Define your application**, select: **Single-page app (SPA)**. The SPA type is appropriate for the React frontend and creates an App Client without a client secret. Then enter the application name under **Name your application**, e.g., `polly-voice`.

![Define the Cognito application](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/02-define-application.png)

4. Under **Options for sign-in identifiers**, select only **E-mail**. Polly Voice uses email addresses for both sign-up and sign-in. Under **Required attributes for sign-up**, select the `name` attribute if allowed. Email is already used as the sign-in identifier.

![Select email sign-in](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/03-email-sign-in.png)

The frontend submits these attributes during sign-up:

```text
email
name
```

5. Under **Add a return URL**, enter the Amplify domain obtained in section 5.4.1:

```text
https://<branch>.<app-id>.amplifyapp.com
```

Then select **Create user directory** to create the Cognito User Pool.

![Configure user attributes](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/04-user-attributes1.png)

## Obtain Cognito Connection Info

1. After creation succeeds, open:

```text
Amazon Cognito
→ User pools
→ Newly created User Pool
→ Overview
```

Locate and record the **User pool ID**. Its format is:

```text
eu-north-1_xxxxxxxxx
```

This value is used for:

```text
VITE_COGNITO_USER_POOL_ID
```

![Cognito User Pool ID](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/06-user-pool-id.png)

7. In the User Pool, open:

```text
Applications
→ App clients
→ polly-voice
```

Locate and record the **Client ID**. This value is used for:

```text
VITE_COGNITO_CLIENT_ID
```

![Cognito App Client ID](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/07-app-client-id.png)

10. Open the `polly-voice` Amplify application and select:

```text
Hosting
→ Environment variables
```

![Amplify environment variables](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/08-amplify-environment-variables.png)

Select **Manage variables** and add these variables:

| Variable | Value | Description |
|---|---|---|
| `VITE_AWS_ENABLED` | `true` | Enables Cognito authentication mode on the frontend. |
| `VITE_AWS_REGION` | `eu-north-1` | The region containing the Cognito User Pool. |
| `VITE_COGNITO_USER_POOL_ID` | `eu-north-1_xxxxxxxxx` | The ID of the newly created User Pool. |
| `VITE_COGNITO_CLIENT_ID` | `<APP_CLIENT_ID>` | The ID of the `polly-voice` SPA App Client. |

Then select **Save** to save the variables.

![Amplify Cognito environment variables](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/10-amplify-environment-variables.png)

11. After saving the environment variables, open **Overview**, select the **Production branch**, and select **Redeploy this version**.

Redeploying is required because Vite injects `VITE_*` variables into the JavaScript bundle during the build phase.

![Redeploy the Amplify frontend](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/11-redeploy-frontend.png)
![Redeploy the Amplify frontend](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/11-redeploy-frontend1.png)

## Backend Configuration Synchronization

12. The backend must verify JWTs from the same User Pool and App Client as the frontend. Verify the Lambda environment variables:

```text
AWS_COGNITO_USER_POOL_ID
AWS_COGNITO_CLIENT_ID
AWS_COGNITO_ISSUER_URI
```

Issuer URI format:

```text
https://cognito-idp.eu-north-1.amazonaws.com/<USER_POOL_ID>
```

![Lambda Cognito environment variables](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/12-lambda-cognito-configuration.png)

## Test Sign-Up and Sign-In

13. Open the Amplify production website and select **Sign up**. Enter:

- Display name.
- Email.
- Password meeting password policy.

![Register a Polly Voice account](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/13-sign-up-form.png)

14. Enter the confirmation code and select confirm.

![Confirm the email address](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/14-confirm-email.png)

15. After confirmation, sign in with email and password. Successful sign-in will show user info in header.

![Cognito sign-in succeeded](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/15-sign-in-success.png)

16. Open Cognito Console → **Users**, and verify user status:

```text
CONFIRMED
```

![Confirmed Cognito user](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/16-confirmed-user.png)

17. Open Developer Tools → **Network**, make an authenticated request, and verify header:

```http
Authorization: Bearer <access-token>
```

![Authenticated API request](/images/5-Workshop/5.4-DeployFrontend/5.4.2-cognito/17-authenticated-request.png)

## Common Issues

| Issue | Cause | Resolution |
|---|---|---|
| `User is not confirmed` | User hasn't entered confirmation code | Complete confirmation form or resend code |
| `User does not exist` | Email belongs to a different User Pool | Verify `VITE_COGNITO_USER_POOL_ID` |
| `Incorrect username or password` | Invalid credentials | Check email/password or reset password |
| `Invalid client` | Incorrect App Client ID | Use SPA App Client without client secret |
| API returns HTTP 401 | Frontend/Backend User Pool mismatch | Synchronize Amplify & SAM parameters |
| Website uses old config | Saved variables without rebuilding | Redeploy Amplify branch |
| Email not received | Check email address or spam folder | Resend confirmation code |

## Result

Amazon Cognito is connected to the Polly Voice frontend. Users can sign up, confirm email, sign in, and sign out on the Amplify website. The frontend receives access tokens from Cognito and sends them to API Gateway for backend verification.
