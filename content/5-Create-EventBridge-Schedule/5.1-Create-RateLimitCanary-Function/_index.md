---
title : "Create Lambda to Manage API Stage"
date :  "2025-09-03" 
weight : 1
chapter : false
pre : " <b> 5.1 </b> "
---

**Contents:**
- [Create RateLimitCanary Lambda Function](#create-ratelimitcanary-lambda-function)
- [Test the Lambda](#test-the-lambda)

## Create RateLimitCanary Lambda Function

- Go to the [Lambda](https://console.aws.amazon.com/lambda/) service
- Click **Create function**

![Lambda Function](/images/2/0006.png?featherlight=false&width=90pc)

- In the **Create function** interface:
- Select **Author from scratch**
- In **Function name**, enter the function name (RateLimitCanary)
- Select Runtime **Python 3.13**
- Select Architecture **x86_64**
- Click **Create function**

![Lambda Function](/images/5/0001.png?featherlight=false&width=90pc)

- In the code editor of the Lambda function:
- The **RateLimitCanary Lambda** will automatically update throttling (rate limit & burst limit) for a Stage in AWS API Gateway using JSON data sent to it, and send the result (success or error) via a Slack webhook.
- Replace the code with the following:

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
                    "text": f"*API Throttling Update*\n*API_ID*: {api_id}\n*Stage*: {stage_name}\n*RateLimit*: {rate_limit} req/s\n*BurstLimit*: {burst_limit}"
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
    api_id = event.get('API_ID', os.environ.get('API_ID', ''))
    stage_name = event.get('STAGE_NAME', os.environ.get('STAGE_NAME', ''))
    rate_limit = int(event.get('RATE_LIMIT', os.environ.get('RATE_LIMIT', '100')))
    burst_limit = int(event.get('BURST_LIMIT', os.environ.get('BURST_LIMIT', '200')))
    
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

1. **Required Libraries**
- **boto3**: SDK to call AWS APIs (here we use API Gateway)
- **urllib3**: to send HTTP requests (used to call Slack webhook)

2. **send_slack_notification function**
- Sends a POST request to the webhook with a notification

3. **Lambda handler function**

```python
def lambda_handler(event, context):
    apigw = boto3.client('apigateway')
```

- Creates an API Gateway client to call the update stage API

```python
    api_id = event.get('API_ID', os.environ.get('API_ID', ''))
    stage_name = event.get('STAGE_NAME', os.environ.get('STAGE_NAME', ''))
    rate_limit = int(event.get('RATE_LIMIT', os.environ.get('RATE_LIMIT', '100')))
    burst_limit = int(event.get('BURST_LIMIT', os.environ.get('BURST_LIMIT', '200')))
```

- Prefers values from the event (payload when invoking Lambda)
- If not found → fallback to environment variables

```python
if not all([api_id, stage_name]):
        error_message = "Missing required parameters: API_ID or STAGE_NAME"
        send_slack_notification(error_message, api_id or "N/A", stage_name or "N/A", rate_limit, burst_limit)
        raise ValueError(error_message)
```

- If API_ID or STAGE_NAME is missing → send error to Slack + raise exception

```python
apigw.update_stage(
        restApiId=api_id,
        stageName=stage_name,
        patchOperations=[
            {'op': 'replace','path': '/*/*/throttling/rateLimit','value': str(rate_limit)},
            {'op': 'replace','path': '/*/*/throttling/burstLimit','value': str(burst_limit)}
        ]
    )

- Calls API Gateway’s **update_stage** API to change the configuration

#### Test the Lambda

- In the interface of the RateLimitCanary Lambda function, select the **Test** tab
- Choose **Create new event**
- Paste the following JSON values and click **Test**:

```json
{ "API_ID":"jbjfqykyob", "STAGE_NAME":"test", "RATE_LIMIT":50, "BURST_LIMIT":100 }
```

![Lambda Function](/images/5/0002.png?featherlight=false&width=90pc)
