+++
title = '5.1 Overview'
weight = 1

[params]
collapsibleMenu = true
+++

## Overview

In this workshop, we will build **Polly Voice**, a cloud-native speech processing web application using Serverless technologies on **Amazon Web Services (AWS)**.

The application provides two main features:

* **Text-to-Speech (TTS):** Convert text into natural-sounding speech using **Amazon Polly**.
* **Speech-to-Text (STT):** Convert speech into text using **Amazon Transcribe**.

Users can securely sign in, generate audio files from text, upload audio files for transcription, store audio files in **Amazon S3**, and manage their conversion history through **Amazon DynamoDB**.

Instead of deploying and managing traditional servers, the entire application is built using fully managed AWS services, enabling automatic scaling, reducing operational costs, and simplifying infrastructure management.

This workshop demonstrates how multiple AWS services can be integrated to build a complete cloud application using a serverless architecture while following AWS security best practices.

---

## Learning Objectives

After completing this workshop, you will be able to:

* Understand how to design a Serverless application on AWS.
* Configure Amazon Cognito for user authentication.
* Build REST APIs using Amazon API Gateway and AWS Lambda.
* Convert text into speech using Amazon Polly.
* Convert speech into text using Amazon Transcribe.
* Store audio files in Amazon S3.
* Store application data in Amazon DynamoDB.
* Deploy a React application using AWS Amplify.
* Secure APIs using JWT authentication.
* Apply the Principle of Least Privilege (IAM Least Privilege).
* Test and validate the complete application workflow.

---

## Workshop Architecture

The Polly Voice application uses the following AWS services:

* AWS Amplify
* Amazon Cognito
* Amazon API Gateway
* AWS Lambda
* Amazon Polly
* Amazon Transcribe
* Amazon S3
* Amazon DynamoDB

Each service is responsible for a specific function within the system and works together to form a complete Serverless architecture. **Amazon Polly** is responsible for converting text into speech (TTS), while **Amazon Transcribe** performs speech-to-text (STT) conversion.
