+++
title = '5.2 Prerequisites'
weight = 2

[params]
collapsibleMenu = true
+++

## Prerequisites

Before starting this workshop, we need to prepare the AWS environment and install several development tools. The following prerequisites will ensure that the deployment and testing process runs smoothly.

---

## AWS Account

An active AWS account with sufficient permissions to create and manage AWS resources is required.

This workshop will use the following AWS services:

* AWS Amplify
* Amazon Cognito
* Amazon API Gateway
* AWS Lambda
* Amazon Polly
* Amazon Transcribe
* Amazon S3
* Amazon DynamoDB
* AWS Identity and Access Management (IAM)
* Amazon CloudWatch

---

## AWS Region

Throughout this workshop, we will use the following AWS Region:

```text
ap-southeast-1 (Singapore)
```

Using the same AWS Region for all services simplifies deployment and minimizes compatibility issues between services.

---

## Required IAM Permissions

Your AWS account should have permission to create and manage the following resources:

* IAM Roles
* Amazon Cognito User Pools
* AWS Lambda Functions
* Amazon API Gateway
* Amazon S3 Buckets
* Amazon DynamoDB Tables
* AWS Amplify Applications
* Amazon Polly
* Amazon Transcribe
* Amazon CloudWatch Logs

To improve security, follow the **Principle of Least Privilege** by granting only the permissions required to complete this workshop.

---

## Development Environment

Before deploying the application, install the following software.

### Node.js

Install the latest **Node.js LTS** version together with **npm**.

The frontend application is developed using **React** and **Vite**.

---

### Visual Studio Code

It is recommended to use **Visual Studio Code** as the source code editor.

Useful extensions include:

* ESLint
* Prettier
* AWS Toolkit

---

### Git

Git is used for version control and source code management.

It is also recommended to create a GitHub repository to back up the project source code and support future deployments.

---

## Project Structure

The project is divided into two main components:

```text
Polly Voice
│
├── frontend
│   ├── React
│   ├── TypeScript
│   └── Vite
│
└── backend
    ├── AWS Lambda
    ├── Amazon Polly
    ├── Amazon Transcribe
    ├── Amazon S3
    ├── Amazon DynamoDB
    └── API Gateway
```

Separating the frontend and backend makes the application easier to develop, deploy, and maintain.

---

## Architecture Overview

After deployment, the application will operate according to the following workflow:

```text
User
   │
   ▼
AWS Amplify
   │
   ▼
Amazon Cognito
   │
   ▼
API Gateway
   │
   ▼
AWS Lambda
   │
   ├────────► Amazon Polly
   │              │
   │              ▼
   │         Speech Audio
   │
   ├────────► Amazon Transcribe
   │              │
   │              ▼
   │      Transcribed Text
   │
   ├────────► Amazon S3
   │
   └────────► Amazon DynamoDB
```

Users are authenticated through **Amazon Cognito** before sending requests to **Amazon API Gateway**. **AWS Lambda** then processes the requests by:

* Calling **Amazon Polly** to convert text into speech (Text-to-Speech).
* Calling **Amazon Transcribe** to convert speech into text (Speech-to-Text).
* Storing audio files in **Amazon S3**.
* Recording conversion history in **Amazon DynamoDB**.
* Returning the results to the frontend application.

---

## Before You Begin

Before proceeding to the next section, make sure that:

* You have an active AWS account.
* All required AWS services are available in the selected Region.
* Node.js and Visual Studio Code are installed.
* The project source code has been prepared.
* You understand the overall architecture of the application.

Once these prerequisites are complete, we are ready to move on to designing and deploying the solution architecture.
