---
title : "Create CloudWatch Alarm and Dashboard"
date :  "2025-09-03" 
weight : 6
chapter : false
pre : " <b> 6.2 </b> "
---

**Contents:**
- [Set up CloudWatch Dashboard](#set-up-cloudwatch-dashboard)
- [Create CloudWatch Alarm](#create-alarm)
- [Add Invoke Permission for Alarm](#add-invoke-permission-for-alarm)
- [Test Alarm](#test-alarm)

## Set up CloudWatch Dashboard

- Go to [CloudWatch Dashboard](https://console.aws.amazon.com/cloudwatch/home#dashboards/)
- Click **Create dashboard**

![Alarm](/images/7/00016.png?featherlight=false&width=90pc)

- Enter a name in **Dashboard name**
- Click **Create dashboard**

![Alarm](/images/7/00017.png?featherlight=false&width=90pc)

- In the dashboard interface, click the **+** icon in the top right corner

![Alarm](/images/7/0001.png?featherlight=false&width=90pc)

- In the **Add widget** interface, set **Data source types** to **CloudWatch**
- Choose **Metrics** → **Line**
- Click **Next**

![Alarm](/images/7/0002.png?featherlight=false&width=90pc)

- In **Add metric graph**, select ApiGateway → By Stage → choose the metrics you want
- Click **Create widget**

![Alarm](/images/7/0003.png?featherlight=false&width=90pc)
![Alarm](/images/7/0004.png?featherlight=false&width=90pc)
![Alarm](/images/7/0005.png?featherlight=false&width=90pc)

- If you’re satisfied with your dashboard, click **Save**

![Alarm](/images/7/0006.png?featherlight=false&width=90pc)

## Create Alarm

- Go to [CloudWatch Alarm](https://console.aws.amazon.com/cloudwatch/home#alarmsV2)
- Click **Create Alarm**

![Alarm](/images/7/00018.png?featherlight=false&width=90pc)

- In **Step 1** of **Create alarm**, select **Select metric** → ApiGateway → By Stage → choose **Count** of stage test → Click **Select metric**

![Alarm](/images/7/00019.png?featherlight=false&width=90pc)
![Alarm](/images/7/0007.png?featherlight=false&width=90pc)

- In the next interface, set **Statistic** to **Sum**, **Threshold** to Static, and **Whenever Count is... Greater Than... 10** (for testing the function)  
- Click **Next**

![Alarm](/images/7/0008.png?featherlight=false&width=90pc)

- In **Step 2**, set **Alarm state trigger** to **In alarm**
- Choose **Create new topic**, give it a name, enter your email, and click **Create topic**

![Alarm](/images/7/0009.png?featherlight=false&width=90pc)

- Add a **Lambda action** and select the ForceChangeLimit Lambda
- Click **Next**

![Alarm](/images/7/00010.png?featherlight=false&width=90pc)

- In **Step 3**, name the alarm and click **Next**

![Alarm](/images/7/00011.png?featherlight=false&width=90pc)

- In **Step 4**, review and click **Create alarm**

![Alarm](/images/7/00020.png?featherlight=false&width=90pc)

## Add Invoke Permission for Alarm

- Go to the ForceChangeLimit Lambda and click the **Configuration** tab

![Alarm](/images/7/00012.png?featherlight=false&width=90pc)

- Select **Permission** → under **Resource-based policy statements**, click **Add permissions**

![Alarm](/images/7/00013.png?featherlight=false&width=90pc)

- Set **Service** to **Other**
- Enter **StatementID**
- Set **Principal** to **lambda.alarms.cloudwatch.amazonaws.com**
- **Source ARN** is the ARN of the alarm you just created
- **Action** → **lambda:InvokeFunction**
- Click **Save**

{{% notice info %}}
**If this action is missing, the alarm will not be able to trigger the Lambda function**
{{% /notice %}}

![Alarm](/images/7/00014.png?featherlight=false&width=90pc)

## Test Alarm

- Send requests through Postman that exceed the allowed request limit and check the results. If the notification is sent successfully to Slack and the alarm changes to **In Alarm**, it means the alarm is working properly.

![Alarm](/images/7/00015.png?featherlight=false&width=90pc)
