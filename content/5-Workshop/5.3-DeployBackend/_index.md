---
title: "Backend Deployment"
date: 2026-07-30
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

In this section, the AWS services for the Polly Voice Node.js/Express backend
are configured and validated before the application is packaged and deployed as
a serverless application with AWS SAM.

This section includes:

1. Configuring backend AWS services with the AWS Management Console.
2. Deploying the backend with AWS SAM and CloudFormation.
3. Verifying API Gateway, Lambda, and the deployed backend resources.

The pre-deployment section favors Console configuration to demonstrate IAM, S3,
DynamoDB, Polly, Transcribe, Lambda, API Gateway, Cognito, and CloudWatch. Source
code contains application logic only; service settings and credentials are not
hard-coded.

The deployed backend uses:

- Amazon API Gateway HTTP API as the public endpoint.
- AWS Lambda to run Node.js/Express.
- Amazon Polly for speech synthesis.
- Amazon Transcribe to convert media into text.
- Amazon S3 to store media and transcripts.
- Amazon DynamoDB to store processing history.
- Amazon CloudWatch and AWS X-Ray for observability.
