---
title : "Create EventBridge Schedule"
date :  "2025-09-03" 
weight : 2
chapter : false
pre : " <b> 5.2 </b> "
---

**Contents:**
- [Create RateLimitCanary Lambda Function](5.1-Create-RateLimitCanary-Function/)
- [Create EventBridge Schedule](5.2-Create-EventBridge-Schedule/)

## Create Schedule

- Go to the [EventBridge Scheduler](https://console.aws.amazon.com/scheduler/) service
- Click **Create schedule**

![Schedule](/images/6/0009.png?featherlight=false&width=90pc)

- In **Step 1** of the **Create schedule** interface, you can configure as you like. Here is an example setup:

![Schedule](/images/6/0008.png?featherlight=false&width=90pc)
![Schedule](/images/6/0007.png?featherlight=false&width=90pc)
![Schedule](/images/6/0006.png?featherlight=false&width=90pc)

- In **Step 2** of the **Create schedule** interface: select **Target API** as **AWS Lambda** -> choose the **RateLimitCanary Lambda Function** -> Paste the Payload:

```json
{ "API_ID":"jbjfqykyob", "STAGE_NAME":"test", "RATE_LIMIT":100, "BURST_LIMIT":200 }
```

- This payload will be sent to **RateLimitCanary**, and the values will be applied to the stage specified in the payload.

![Schedule](/images/6/0005.png?featherlight=false&width=90pc)
![Schedule](/images/6/0004.png?featherlight=false&width=90pc)

- In **Step 3** of the **Create schedule** interface:

![Schedule](/images/6/0003.png?featherlight=false&width=90pc)
![Schedule](/images/6/0002.png?featherlight=false&width=90pc)

- In **Step 4** of the **Create schedule** interface: review the configuration and click **Create schedule**

![Schedule](/images/6/0001.png?featherlight=false&width=90pc)

Similarly, you can create Schedules for different times to increase or decrease the request traffic of the API stage.

- When the Schedule is triggered, a notification will be sent via Slack.

![Schedule](/images/6/00010.png?featherlight=false&width=90pc)
