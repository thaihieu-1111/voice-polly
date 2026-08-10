---
title: "Clean Up"
date: 2026-07-30
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

# Clean Up

Delete resources that are no longer required to prevent unexpected cost. Work
in **Europe (Stockholm) – `eu-north-1`** and verify every resource name before
deleting it.

{{% notice danger %}}
Cleanup removes the API, Lambda function, media, transcripts, and history.
Download anything that must be retained and do not delete shared resources.
{{% /notice %}}

## 1. Inventory resources

Record the resources in the `polly-voice-api` CloudFormation stack:

- API Gateway HTTP API.
- Lambda function and execution role.
- S3 media bucket.
- DynamoDB history table.
- CloudWatch log groups and alarms.
- Cognito User Pool/App Client.
- Amplify app when the frontend must also be removed.

![Resource inventory](/images/5-Workshop/5.6-Cleanup/01-resource-inventory.png)

## 2. Empty the S3 bucket

The template uses `DeletionPolicy: Retain`; therefore, the bucket may remain
after stack deletion. Download required files, choose **Empty**, and remove all
object versions and delete markers when versioning is enabled. Keep the empty
bucket until the stack has been removed.

![Empty the S3 bucket](/images/5-Workshop/5.6-Cleanup/02-empty-s3-bucket.png)

## 3. Delete CloudWatch alarms

Open **CloudWatch** → **Alarms** → **All alarms**, select the alarms created for
the Polly Voice Lambda and API Gateway, and choose **Actions** → **Delete**.
Remove manually created dashboards when they are no longer required.

![Delete CloudWatch alarms](/images/5-Workshop/5.6-Cleanup/03-delete-alarms.png)

## 4. Delete the CloudFormation stack

Open **CloudFormation**, select `polly-voice-api`, review **Resources**, choose
**Delete**, and follow **Events** until deletion completes. API Gateway and
Lambda are removed with the stack. S3 and DynamoDB can remain because of their
retention policies.

![Delete the CloudFormation stack](/images/5-Workshop/5.6-Cleanup/04-delete-stack.png)

For `DELETE_FAILED`, inspect the failed resource in **Events**, resolve the
dependency or permission problem, and retry deletion.

## 5. Delete retained resources

### S3

Confirm that the bucket is empty, choose **Delete**, enter its exact name, and
confirm.

![Delete the S3 bucket](/images/5-Workshop/5.6-Cleanup/05-delete-bucket.png)

### DynamoDB

Open **DynamoDB** → **Tables**, select `polly-voice-history`, create a backup if
needed, and delete the table.

![Delete the DynamoDB table](/images/5-Workshop/5.6-Cleanup/06-delete-table.png)

## 6. Remove remaining logs

In **CloudWatch** → **Logs** → **Log groups**, delete the workshop-only Lambda
and API Gateway log groups. Export logs first when they are required as report
evidence.

## 7. Delete manually created resources

Delete development resources that were created outside SAM/CloudFormation:

- `polly-voice-api-dev` Lambda.
- `polly-voice-http-api-dev` API Gateway.
- Unused IAM inline policies and execution roles.
- Unneeded Transcribe test jobs.

Detach a role from Lambda and remove its policies before deleting the role.

## 8. Optional frontend and identity cleanup

When the complete application must be removed, delete the Amplify app and then
the Cognito User Pool. Deleting the User Pool permanently deletes its users.
Keep Cognito and Amplify when only the backend is being stopped temporarily.

## 9. Final verification

Confirm that:

- The `polly-voice-api` stack no longer exists.
- Its API Gateway and Lambda function no longer exist.
- The retained S3 bucket and DynamoDB table were deleted or intentionally kept.
- Unneeded alarms and log groups are gone.
- No Transcribe job is running.
- Amplify and Cognito match the intended cleanup scope.
- Billing does not show unexpected continuing usage.

![Cleanup completed](/images/5-Workshop/5.6-Cleanup/07-cleanup-complete.png)

