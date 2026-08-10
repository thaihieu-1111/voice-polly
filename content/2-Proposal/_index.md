+++
title = '2. Proposal'
weight = 2

[params]
collapsibleMenu = true
+++

## Polly Voice

A Speech Processing Application Powered by AWS Serverless

---

## 1. Project Overview

### Objective

Polly Voice is a web application designed to provide two speech processing capabilities:

* **Text-to-Speech (TTS):** Convert text into speech using **Amazon Polly**.
* **Speech-to-Text (STT):** Convert speech into text using **Amazon Transcribe**.

Users can sign in to the system, enter text to generate audio files, or upload audio files for speech recognition. The generated results can be played, downloaded, or reviewed later through the conversion history.

The application is built entirely on a Serverless architecture using Amazon Web Services (AWS), reducing operational costs, enabling easy scalability, and eliminating the need to manage servers.

---

## 2. Problem Statement

### Current Challenges

Today, many users need to convert between text and speech for learning, document reading, content creation, meeting transcription, or assisting visually impaired users. However:

* Many Text-to-Speech and Speech-to-Text services require paid subscriptions.
* Some platforms do not fully support multiple languages or voice options.
* Building a traditional speech processing system requires deploying and maintaining servers, which increases both complexity and cost.

### Proposed Solution

Polly Voice uses:

* **Amazon Polly** for Text-to-Speech conversion.
* **Amazon Transcribe** for Speech-to-Text conversion.

The system adopts a Serverless architecture consisting of:

* Amazon Cognito for user management.
* React running on AWS Amplify.
* Amazon API Gateway for handling frontend requests.
* AWS Lambda for business logic processing.
* Amazon Polly for Text-to-Speech.
* Amazon Transcribe for Speech-to-Text.
* Amazon S3 for audio file storage.
* Amazon DynamoDB for conversion history.

Users simply sign in, then either enter text to generate speech or upload an audio file for transcription.

### Benefits

* No server management required.
* Low operational cost.
* Easy to scale as the number of users grows.
* Fast processing performance.
* Supports both Text-to-Speech and Speech-to-Text features.
* Can be extended for various AI and educational applications in the future.

---

## 3. Solution Architecture

![Architecture Diagram](https://super-chickens-aws.github.io/dinhhhiuu/images/worklog53.png)

### AWS Services Used

* AWS Amplify: Deploys the React frontend.
* Amazon Cognito: Handles user registration and authentication.
* Amazon API Gateway: Provides REST APIs.
* AWS Lambda: Executes business logic.
* Amazon Polly: Converts text into speech.
* Amazon Transcribe: Converts speech into text.
* Amazon S3: Stores audio files.
* Amazon DynamoDB: Stores conversion history.

### Workflow

#### Text-to-Speech Workflow

1. The user signs in using Amazon Cognito.
2. The frontend sends a request to Amazon API Gateway.
3. API Gateway invokes AWS Lambda.
4. Lambda calls Amazon Polly to synthesize speech.
5. The generated MP3 file is stored in Amazon S3.
6. Lambda stores the conversion history in Amazon DynamoDB.
7. The audio file URL is returned to the frontend.
8. The user can play or download the audio file.

#### Speech-to-Text Workflow

1. The user signs in using Amazon Cognito.
2. The frontend uploads an audio file through Amazon API Gateway.
3. API Gateway forwards the request to AWS Lambda.
4. Lambda uploads the audio file to Amazon S3.
5. Lambda calls Amazon Transcribe to transcribe the audio.
6. Amazon Transcribe returns the transcription result.
7. Lambda stores the conversion history in Amazon DynamoDB.
8. The transcribed text is returned to the frontend for display.

---

## 4. Timeline

### Phase 1

* Study Amazon Polly.
* Study Amazon Transcribe.
* Learn the Serverless architecture.
* Design the system architecture.

### Phase 2

* Develop the backend.
* Create AWS Lambda functions.
* Integrate Amazon API Gateway.
* Integrate Amazon Polly.
* Integrate Amazon Transcribe.
* Store audio files in Amazon S3.

### Phase 3

* Develop the React frontend.
* Integrate Amazon Cognito.
* Connect the frontend to the backend.
* Complete both the Text-to-Speech and Speech-to-Text features.

### Phase 4

* Perform system testing.
* Finalize the user interface.
* Deploy the application using AWS Amplify.

---

## 5. Budget

The application is developed using AWS Free Tier services during the development phase.

Estimated monthly cost for a small-scale deployment:

| Service            | Monthly Cost |
| ------------------ | -----------: |
| Amazon Polly       |    ~0.20 USD |
| Amazon Transcribe  |    ~0.10 USD |
| AWS Lambda         |    ~0.00 USD |
| Amazon API Gateway |    ~0.01 USD |
| Amazon S3          |    ~0.05 USD |
| Amazon DynamoDB    |    ~0.02 USD |
| AWS Amplify        |    ~0.10 USD |
| Amazon Cognito     |    ~0.00 USD |

**Estimated total monthly cost:** approximately **0.48 USD/month**.

---

## 6. Risks

### Potential Risks

* Users may submit text exceeding the supported limit.
* Users may upload unsupported audio file formats.
* AWS Free Tier limits for Amazon Polly and Amazon Transcribe may be exceeded.
* Excessive audio storage may increase Amazon S3 costs.
* User authentication failures.

### Mitigation

* Limit the number of characters for each text conversion request.
* Accept only supported audio file formats.
* Configure IAM permissions following the Principle of Least Privilege.
* Regularly delete or archive old audio files.
* Secure all APIs using Amazon Cognito JWT authentication.

---

## 7. Expected Outcomes

After completion, the system will:

* Allow users to register and sign in.
* Convert text into speech using Amazon Polly.
* Convert speech into text using Amazon Transcribe.
* Play and download MP3 files.
* Display speech transcription results.
* Store the conversion history for both features.
* Be fully deployed on an AWS Serverless architecture.
* Be extensible with additional languages, voice options, and AI services in the future.
