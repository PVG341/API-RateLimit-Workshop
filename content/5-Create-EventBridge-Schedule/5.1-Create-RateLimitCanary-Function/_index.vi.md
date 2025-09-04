---
title : "Tạo Lambda quản lý Stage API"
date :  "2025-09-03" 
weight : 1
chapter : false
pre : " <b> 5.1 </b> "
---

**Nội dung:**
- [Tạo Lambda Function RateLimitCanary](#tạo-lambda-function-ratelimitcanary)
- [Test thử Lambda](#test-thử-lambda)

## Tạo Lambda Function RateLimitCanary

- Truy cập dịch vụ [Lambda](https://console.aws.amazon.com/lambda/)
- Ấn vào **Create function**

![Lambda Function](/images/2/0006.png?featherlight=false&width=90pc)

-Trong giao diện **Create function**
- Chọn **Author from scratch**
- Ở mục **Function name** nhập tên của funtion (RateLimitCanary)
- Chọn Runtime **Python 3.13**
- Chọn Architecture x86_64
- Ấn **Create function**

![Lambda Function](/images/5/0001.png?featherlight=false&width=90pc)

- Trong giao diện code của Lambda Authorizer
- Lambda RateLimitCanary sẽ tự động cập nhật throttling (rate limit & burst limit) cho một Stage trong AWS API Gateway thông qua dữ liệu JSON được gửi đến và thông báo kết quả (thành công hoặc lỗi) qua Slack webhook.
- Thay đổi đoạn code như sau:

{{% notice info %}}
**Xem cách tạo webhook Slack ở đây:** https://000022.awsstudygroup.com/vi/2-prerequiste/2.4-incomingwebhooksslack/
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
1. **Các thư viện cần có**
- **boto3**: SDK để gọi API AWS (ở đây dùng API Gateway)
- **urllib3**: gửi request HTTP (dùng để gọi Slack webhook)

2. Hàm **send_slack_notification**
- Gửi request POST tới webhook kèm thông báo

3. Hàm **Lambda handler**

```python
def lambda_handler(event, context):
    apigw = boto3.client('apigateway')
```
- Tạo client API Gateway để gọi các API update stage.

```python
    api_id = event.get('API_ID', os.environ.get('API_ID', ''))
    stage_name = event.get('STAGE_NAME', os.environ.get('STAGE_NAME', ''))
    rate_limit = int(event.get('RATE_LIMIT', os.environ.get('RATE_LIMIT', '100')))
    burst_limit = int(event.get('BURST_LIMIT', os.environ.get('BURST_LIMIT', '200')))
```

- Ưu tiên lấy giá trị từ event (payload khi invoke Lambda).
- Nếu không có → fallback sang biến môi trường.

```python
if not all([api_id, stage_name]):
        error_message = "Missing required parameters: API_ID or STAGE_NAME"
        send_slack_notification(error_message, api_id or "N/A", stage_name or "N/A", rate_limit, burst_limit)
        raise ValueError(error_message)
```

- Nếu thiếu API_ID hoặc STAGE_NAME → báo lỗi Slack + raise exception.

```python
apigw.update_stage(
        restApiId=api_id,
        stageName=stage_name,
        patchOperations=[
            {'op': 'replace','path': '/*/*/throttling/rateLimit','value': str(rate_limit)},
            {'op': 'replace','path': '/*/*/throttling/burstLimit','value': str(burst_limit)}
        ]
    )
```
- Gọi API update_stage của API Gateway để thay đổi config.



#### Test thử Lambda

- Trong giao diện của Lambda function RateLimitCanary, chọn tab **Test**
- Chọn **Create new event**
- Dán các giá trị JSON sau và ấn **Test**:

```json
{ "API_ID":"jbjfqykyob", "STAGE_NAME":"test", "RATE_LIMIT":50, "BURST_LIMIT":100 }
```

![Lambda Function](/images/5/0002.png?featherlight=false&width=90pc)
