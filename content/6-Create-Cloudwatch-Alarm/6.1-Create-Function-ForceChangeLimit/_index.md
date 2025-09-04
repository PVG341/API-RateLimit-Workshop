---
title : "Create ForceChangeLimit Lambda Function"
date :  "2025-09-03" 
weight : 1
chapter : false
pre : " <b> 6.1 </b> "
---

**Contents:**
- [Create ForceChangeLimit Lambda Function](#create-lambda-function)

## Create Lambda Function

- Go to [Lambda](https://console.aws.amazon.com/lambda/)
- Click **Create function**

![Lambda Function](/images/2/0006.png?featherlight=false&width=90pc)

- In the **Create function** interface:
  - Choose **Author from scratch**
  - In **Function name**, enter the function name (ForceChangeLimit)
  - Select Runtime **Python 3.13**
  - Select Architecture **x86_64**
  - Click **Create function**

![Lambda Function](/images/7/0001.png?featherlight=false&width=90pc)

- In the code editor of the Lambda function:  
  - The ForceChangeLimit Lambda works similarly to RateLimitCanary, but since an Alarm cannot send JSON values to Lambda, we will configure fixed values for this Lambda.  
  - Replace the code as follows:

{{% notice info %}}
**See how to create a Slack webhook here:** https://000022.awsstudygroup.com/vi/2-prerequiste/2.4-incomingwebhooksslack/
{{% /notice %}}

```python
import boto3
import os
import urllib3
import json

webhook_url = "https://hooks.slack.com/services/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"
http = urllib3.PoolManager()

def send_slack_notification(message, api_id, stage_name, rate_limit, burst_limit):
    payload = {
        "text": f":rocket: *API Throttling Update* | API_ID: {api_id} | Stage: {stage_name} | Rate: {rate_limit} req/s | Burst: {burst_limit}",
        "blocks": [
            {
                "type": "section",
                "text": {
                    "type": "mrkdwn",
                    "text": f"*API Throttling Update due to Too much request*\n*API_ID*: {api_id}\n*Stage*: {stage_name}\n*RateLimit*: {rate_limit} req/s\n*BurstLimit*: {burst_limit}"
                }
            }
        ]
    }
    try:
        encoded_data = json.dumps(payload).encode('utf-8')
        r = http.request(
            method="POST",
            url=webhook_url,
            body=encoded_data,
            headers={'Content-Type': 'application/json'}
        )
        if r.status != 200:
            print(f"Failed to send Slack notification: {r.status}, {r.data.decode('utf-8')}")
        else:
            print("Slack notification sent successfully")
    except Exception as e:
        print(f"Error sending Slack notification: {str(e)}")

def lambda_handler(event, context):
    apigw = boto3.client('apigateway')
    
    # Ưu tiên giá trị từ event, nếu không có thì lấy từ biến môi trường
    api_id = "jbjfqykyob"
    stage_name = "test"
    rate_limit = 200
    burst_limit = 400
    
    # Kiểm tra nếu thiếu biến cần thiết
    if not all([api_id, stage_name]):
        error_message = "Missing required parameters: API_ID or STAGE_NAME"
        send_slack_notification(error_message, api_id or "N/A", stage_name or "N/A", rate_limit, burst_limit)
        raise ValueError(error_message)
    
    try:
        apigw.update_stage(
            restApiId=api_id,
            stageName=stage_name,
            patchOperations=[
                {
                    'op': 'replace',
                    'path': '/*/*/throttling/rateLimit',
                    'value': str(rate_limit)
                },
                {
                    'op': 'replace',
                    'path': '/*/*/throttling/burstLimit',
                    'value': str(burst_limit)
                }
            ]
        )
        
        # Gửi thông báo Slack khi cập nhật thành công
        message = f"API Gateway throttling updated: API_ID={api_id}, Stage={stage_name}, RateLimit={rate_limit} req/s, BurstLimit={burst_limit}"
        send_slack_notification(message, api_id, stage_name, rate_limit, burst_limit)
        
        return {
            'statusCode': 200,
            'body': f'Updated throttling: rate={rate_limit}, burst={burst_limit}'
        }
    except Exception as e:
        # Gửi thông báo Slack nếu có lỗi
        error_message = f"Error updating API Gateway throttling: {str(e)}"
        send_slack_notification(error_message, api_id, stage_name, rate_limit, burst_limit)
        raise e
```