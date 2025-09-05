---
title : "Create Usage Plan"
date :  "2025-09-03" 
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---

**Contents:**
- [Create Usage Plan](#create-usage-plan)
- [Add Associated Stages](#add-associated-stages)

A **Usage Plan** in API Gateway is a mechanism to manage how clients consume an API by combining quota, throttling, and API Key, applied to specific stages.

## Create Usage Plan

- Go to [Usage Plans](https://console.aws.amazon.com/apigateway/main/usage-plan) in the left-hand menu of the API Gateway console.  
- Click **Create usage plan**

![Usage Plan](/images/3/00017.png?featherlight=false&width=90pc)

- In the **Create usage plan** interface, enter a name for the plan in **Name**.  
- Enable **Throttling**  
- Enter desired values for **Rate** and **Burst**  
- Enable **Quota**  
- Enter the number of **Requests** as desired (e.g., for normal users set **100 per day**, but here we use **10 requests per day** for easier testing), and choose **Per day** as the interval.  
- Click **Create usage plan**

{{% notice info %}}  
**Rate** is the average rate (requests per second – RPS) allowed by API Gateway, while **Burst** is the maximum number of requests that can be processed instantly during a short spike.  
{{% /notice %}}

![Usage Plan](/images/3/00018.png?featherlight=false&width=90pc)

- Continue creating **Usage Plans** for:  
  - **dev** (rate: 1000, burst: 2000, quota disabled)  
  - **premium user** (rate: 20, burst: 40, quota: 100 requests per day)

![Usage Plan](/images/3/00021.png?featherlight=false&width=90pc)

#### Add Associated Stages

When a stage is associated with a Usage Plan, all requests going through that stage will be subject to the quota/throttling rules you defined.

- Open one of the **Usage Plans**  
- In the **Usage Plan** interface, click **Add stage**

![Usage Plan](/images/3/00019.png?featherlight=false&width=90pc)

- Select **API** as the API you created (MyProjectAPI)  
- Select Stage as **test**  
- In **Method-level throttling**, choose the Resources you created earlier, select Method **GET**, and customize Rate and Burst.  
- Click **Add to usage plan**

![Usage Plan](/images/3/00020.png?featherlight=false&width=90pc)

- Repeat for the other usage plans.

![Usage Plan](/images/3/00022.png?featherlight=false&width=90pc)  
![Usage Plan](/images/3/00023.png?featherlight=false&width=90pc)
