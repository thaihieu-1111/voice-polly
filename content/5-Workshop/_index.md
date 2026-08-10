---

title: "Workshop"
date: 2024-01-01
weight: 5
chapter: false
pre: " <b> 5. </b> "
---

## Overview

**Build the Polly Voice Application on an AWS Serverless Architecture**

**Polly Voice** is a web-based speech processing application built entirely on **Amazon Web Services (AWS)** using a **Serverless** architecture.

The application allows users to register an account, sign in, and use two main features:

* **Text-to-Speech (TTS):** Users can enter text, select a voice, and generate MP3 audio files using **Amazon Polly**.
* **Speech-to-Text (STT):** Users can upload an audio file, and the system uses **Amazon Transcribe** to convert speech into text.

After processing is completed, the generated audio files are stored in **Amazon S3**, while the conversion history for both features is saved in **Amazon DynamoDB**, allowing users to review or download their previous results at any time.

In this workshop, the entire system will be deployed using fully managed AWS services, eliminating the need to manage servers, reducing operational costs, and making it easy to scale as the number of users grows.

Throughout this workshop, you will learn how multiple AWS services work together to build a complete cloud-native web application, including user authentication with **Amazon Cognito**, business logic processing with **AWS Lambda**, communication through **Amazon API Gateway**, text-to-speech conversion with **Amazon Polly**, speech-to-text conversion with **Amazon Transcribe**, data storage using **Amazon S3** and **Amazon DynamoDB**, and frontend deployment with **AWS Amplify**.

#### Contents

1. [Workshop Overview](5.1-Workshop-overview/)
2. [Prerequisites](5.2-Prerequiste/)
3. [Configure and Deploy the Backend](5.3-DeployBackend/)
4. [Deploy the Frontend](5.4-DeployFrontend/)
5. [Testing and System Validation](5.5-TestValidation/)
6. [Clean Up Resources](5.6-Cleanup/)

After completing this workshop, you will successfully deploy a complete **AI Voice** application that supports both **Text-to-Speech** and **Speech-to-Text**, following a modern **AWS Serverless** architecture and AWS security best practices.
