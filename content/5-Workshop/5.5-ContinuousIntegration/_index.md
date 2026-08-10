---
title: "Continuous Integration & Deployment (CI/CD)"
date: 2026-07-31
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

# CI/CD Pipeline Setup

## 1. CI/CD Overview

The CI/CD pipeline for Polly Voice is divided into two distinct responsibilities.
Continuous Integration (CI) verifies code quality through unit testing, typechecking, linting, building, and AWS SAM template validation.
Continuous Deployment (CD) takes the code passing CI and deploys the backend to AWS.

Overall workflow:

```text
Developer
    ↓
GitHub
    ↓
Continuous Integration
    ↓
Test, typecheck, lint, build, and SAM validation
    ↓
Pull Request merged into main
    ↓
Continuous Deployment
    ↓
GitHub OIDC → Temporary AWS credentials
    ↓
SAM deploy → CloudFormation updates backend
    ↓
Verify /health endpoint
```

GitHub Actions acts as the control plane orchestrating the CI/CD steps.

## 2. Prerequisites

Shared prerequisites for CI/CD:

| Component | Configuration |
|---|---|
| AWS account | Account used for backend management and deployment |
| GitHub repository | `polly-voice` |
| Target branch | `main` |
| Backend runtime | Node.js 22 |
| Package manager | npm |
| Version control | Git |
| Local AWS tools | AWS CLI and AWS SAM CLI |
| Infrastructure | AWS SAM and AWS CloudFormation |
| Automation platform | GitHub Actions |
| Backend region | `eu-north-1` |

## 3. Workflow Details

![Repository audit](/images/5-Workshop/5.5-ContinuousIntegration/00-repository-audit.png)
![Pull Request checks](/images/5-Workshop/5.5-ContinuousIntegration/01-pr-checks.png)
![Pull Request merged](/images/5-Workshop/5.5-ContinuousIntegration/02-pr-merged.png)
![GitHub Environment dev](/images/5-Workshop/5.5-ContinuousIntegration/07-github-environment-dev.png)
![GitHub Actions backend CD success](/images/5-Workshop/5.5-ContinuousIntegration/09-backend-cd-success.png)
![CloudFormation stack](/images/5-Workshop/5.5-ContinuousIntegration/10-cloudformation-stack.png)
![Health check response](/images/5-Workshop/5.5-ContinuousIntegration/11-health-check.png)
![Amplify WAF enabled](/images/5-Workshop/5.5-ContinuousIntegration/12-amplify-waf-enabled.png)
![CloudWatch dashboard](/images/5-Workshop/5.5-ContinuousIntegration/13-cloudwatch-dashboard.png)
![CloudWatch alarms](/images/5-Workshop/5.5-ContinuousIntegration/14-cloudwatch-alarms.png)

## 4. Results Achieved

The Polly Voice system has completed the full Continuous Integration and Continuous Deployment pipeline.
- GitHub OIDC eliminated long-lived AWS access keys.
- AWS SAM and CloudFormation provided repeatable infrastructure deployment.
- The `polly-voice-api` stack reached `CREATE_COMPLETE`.
- Backend health check completed successfully.
- AWS WAF protected the frontend deployed on Amplify Hosting.
- CloudWatch Dashboard provided a centralized view of backend performance.
- CloudWatch Alarms and Amazon SNS supported monitoring and alert notifications.
