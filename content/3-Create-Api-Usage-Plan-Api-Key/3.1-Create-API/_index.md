---
title : "Create API"
date :  "2025-09-03" 
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---

**Contents:**
- [Create API](#create-api)
- [Create Resource for API](#create-resource-for-api)
- [Create Method for Resource](#create-method-for-resource)
- [Create Stage for API](#create-stage-for-api)
- [Enable Canary for Stage](#enable-canary-for-stage)

## Create API

Go to [API Gateway](https://console.aws.amazon.com/apigateway/main)

- Click **Create API**

![API Gateway](/images/3/0001.png?featherlight=false&width=90pc)

- Choose **Build** under **REST API**

![API Gateway](/images/3/0002.png?featherlight=false&width=90pc)

- Select **New API**  
- Enter a name in **API name**  
- Select **API endpoint type** as **Regional**  
- Select **IP address type** as IPv4  
- Click **Create API**

![API Gateway](/images/3/0003.png?featherlight=false&width=90pc)

## Create Resource for API

A **Resource in API Gateway** is a **path segment** in a REST API. It is combined with an HTTP method to form a complete endpoint that clients can call.

- In the interface of the newly created API, click **Create resource**

![API Gateway](/images/3/0004.png?featherlight=false&width=90pc)

- Enter a name in **Resource name**  
- Enable **CORS**  
- Click **Create resource**

{{% notice info %}}  
When CORS is enabled, the **OPTIONS** method will be automatically created for the **Resource**.  
{{% /notice %}}

![API Gateway](/images/3/0005.png?featherlight=false&width=90pc)

- Create the following **Resources**: Devs, PremiumUsers, Users (Enable CORS for each resource).

## Create Method for Resource

- Select a Resource (e.g., /Devs) and click **Create method**

![Resource Method](/images/3/0006.png?featherlight=false&width=90pc)

- In the **Create method** interface, select **Method type** as **GET**  
- **Integration type**: **Lambda function**  
- Select the corresponding **Lambda Function** for the **Resource** (e.g., choose **devFunction** for **Resource /Devs**)  
- Click **Create method**

![Resource Method](/images/3/0007.png?featherlight=false&width=90pc)

- Repeat the process to create **Methods** for the remaining **Resources**.

![Resource Method](/images/3/0008.png?featherlight=false&width=90pc)

## Create Stage for API

A **Stage** in **API Gateway** is a **deployment environment** of an API. It has its own configuration, URL endpoint, and stage variables, commonly used to differentiate between dev/test/prod (e.g., prod stage: https://abc123.execute-api.ap-southeast-1.amazonaws.com/prod).

{{% notice info %}}  
You can create a Stage either in the **Resources** interface or the **Stages** interface of the API.  
{{% /notice %}}

### Create Stage in the Resources Interface

- Click **Deploy API** in the Resources interface.

![API Stage](/images/3/0009.png?featherlight=false&width=90pc)

- In the menu that appears, select **New stage** in the **Stage** section.  
- Enter a name for the Stage in **Stage name** (e.g., **test**)  
- Click **Deploy**

![API Stage](/images/3/00010.png?featherlight=false&width=90pc)

### Create Stage in the Stages Interface

- Click **Create stage** in the **Stages** interface.

![API Stage](/images/3/00012.png?featherlight=false&width=90pc)

- In the **Create stage** interface, enter a name in **Stage name**  
- Choose a Deployment in the **Deployment** section  
- In **Additional settings**, enable **Throttling** and configure **Rate** and **Burst** as desired  
- Click **Create stage**

![API Stage](/images/3/00013.png?featherlight=false&width=90pc)

- After **Deploy**, you can access the API using the **Invoke URL** of the methods.

![API Stage](/images/3/00011.png?featherlight=false&width=90pc)

## Enable Canary for Stage

A Canary in AWS API Gateway stage is a mechanism that allows you to perform a canary release – deploying a new version of the API to a small portion of traffic before rolling it out to all users.

- In the stage interface, click **Canary**  
- Click **Create Canary**

![Stage Canary](/images/3/00014.png?featherlight=false&width=90pc)

- In the **Create Canary** interface, you can configure options such as **Request distribution** or **Stage cache** (in this workshop, cache will not be used for canary).  
- Click **Create canary**

![Stage Canary](/images/3/00015.png?featherlight=false&width=90pc)

{{% notice note %}}  
Remember to **Promote Canary** before using it, so that requests go through the new version of the Stage (otherwise you may wonder why your changes don’t appear).  
{{% /notice %}}

![Stage Canary](/images/3/00016.png?featherlight=false&width=90pc)

{{% notice info %}}  
**Request distribution** in API Gateway canary defines how client requests are split between:  
- **Production stage** (the current stable version)  
- **Canary stage** (the new version under testing).  
{{% /notice %}}
