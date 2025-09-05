---
title : "Create API Key"
date :  "2025-09-03" 
weight : 3
chapter : false
pre : " <b> 3.3 </b> "
---

**Contents:**
- [Create API Key](#create-api-key)
- [Assign API Key to Usage Plan](#assign-api-key-to-usage-plan)
- [Add API Key to Method](#add-api-key-to-method)


## Create API Key

- Go to [API Keys](https://console.aws.amazon.com/apigateway/main/api-keys) in the left-hand menu of the API Gateway console.  
- Click **Create API Key**

![API Key](/images/3/00024.png?featherlight=false&width=90pc)

- In the **Create API Key** interface, enter a name for the API Key in the **Name** field.  
- Select **Auto-generate**  
- Click **Save**

![API Key](/images/3/00025.png?featherlight=false&width=90pc)

- Create two additional API Keys for User and Premium user: `normalUserKey`, `premiumUser`

## Assign API Key to Usage Plan

- Open a usage plan, then select **Associated API Keys**  
- Click **Add API Key**

![API Key](/images/3/00026.png?featherlight=false&width=90pc)

- In the **Add API Key** interface, select **Add existing key** and choose the API Keys corresponding to your usage plan.  

![API Key](/images/3/00027.png?featherlight=false&width=90pc)

- Repeat the same steps for the remaining usage plans.

## Add API Key to Method

- Open the **Method request** tab of the GET method in the Resources menu.  
- Click **Edit**

![API Key](/images/3/00028.png?featherlight=false&width=90pc)

- In the **Edit method request** interface, enable **API Key required**  
- Click **Save**

![API Key](/images/3/00029.png?featherlight=false&width=90pc)

- Repeat the same steps for the other GET methods.
