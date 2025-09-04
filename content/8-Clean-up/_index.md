---
title : "Clean Up Resources"
date :  "2025-09-03" 
weight : 8
chapter : false
pre : " <b> 8. </b> "
---

**Contents**
- [Delete S3](#delete-s3)
- [Delete EventBridge Schedule](#delete-eventbridge-schedule)
- [Delete Cloudwatch Alarm](#delete-cloudwatch-alarm)
- [Delete API Gateway](#delete-api-gateway)
- [Delete Lambda](#delete-lambda)
- [Delete IAM Role](#delete-iam-role)

Now let’s go through the steps required to delete the created resources.

## Delete S3

1. Go to [S3](https://console.aws.amazon.com/lambda/)
- Select the created S3 bucket
- Click **Empty**
- Type **permanently delete**, then click **Empty** to delete the objects inside the bucket
- Click **Exit**

2. After all objects are deleted, click **Delete**
- Enter the bucket name and click **Delete bucket**

## Delete EventBridge Schedule

- Go to [EventBridge Scheduler](https://console.aws.amazon.com/scheduler/)
- Select the Schedule and click **Delete**
- Confirm **Delete**

## Delete Cloudwatch Alarm

- Go to [Cloudwatch Alarm](https://console.aws.amazon.com/cloudwatch/home#alarmsV2)
- Select the Alarm
- Click **Action** -> choose **Delete**
- Confirm **Delete**

## Delete API Gateway

1. Delete API

- Go to [API Gateway](https://console.aws.amazon.com/apigateway/main)
- Select the API -> Click **Delete**

2. Delete Usage Plan

- Go to [Usage plans](https://console.aws.amazon.com/apigateway/main/usage-plans)
- Select the usage plans -> Click **Delete**
- Type **confirm** and click **Delete**

3. Delete API Key

- Go to [API Keys](https://console.aws.amazon.com/apigateway/main/api-keys)
- Select the API Keys -> Click **Action**
- Choose **Delete** -> Confirm **Delete**

## Delete Lambda

- Go to [Lambda](https://console.aws.amazon.com/lambda/)
- Select the Lambda functions -> Click **Action**
- Choose **Delete** -> type **confirm** -> Click **Delete**

## Delete IAM Role

- Go to [IAM Role](https://console.aws.amazon.com/iam/home/roles)
- Select the role and click **Delete**
- Type the role name and click **Delete**
