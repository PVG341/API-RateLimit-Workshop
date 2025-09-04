---
title : "Create Authorizer"
date :  "2025-09-03" 
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

**Contents:**
- [Create Lambda Function Authorizer](#create-lambda-function-authorizer)
- [Integrate with API](#integrate-with-api)
- [Set Authorizer for Methods](#set-authorizer-for-methods)

An **Authorizer** in API Gateway is a mechanism that allows you to control **authentication** and **request authorization (based on token or policy)** before the request is forwarded to the backend.

## Create Lambda Function Authorizer

- Go to the [Lambda](https://console.aws.amazon.com/lambda/) service  
- Click **Create function**

![Lambda Authorizer](/images/2/0006.png?featherlight=false&width=90pc)

- In the **Create function** interface:  
  - Select **Author from scratch**  
  - In **Function name**, enter the function name (Authorizer)  
  - Select Runtime **Python 3.13**  
  - Select Architecture **x86_64**  
  - Click **Create function**

![Lambda Authorizer](/images/4/0001.png?featherlight=false&width=90pc)

- In the code editor of the Lambda Authorizer:  
  - This Authorizer will validate the **authorizationToken** (sent by the client). Based on the token, it decides whether to **Allow** or **Deny** API calls. If allowed, it only grants access to specific resources (endpoints).  
  - Update the code as follows:  

```python
def generate_policy(principal_id, effect, resources, context=None):
    if not isinstance(resources, list):
        resources = [resources]

    policy = {
        "principalId": principal_id,
        "policyDocument": {
            "Version": "2012-10-17",
            "Statement": [{
                "Action": "execute-api:Invoke",
                "Effect": effect,
                "Resource": resources
            }]
        }
    }
    if context:
        policy["context"] = context
    return policy

user_type_config = {
    "normalUser": {
        "resource": "Users"
    },
    "premiumUser": {
        "resource": "PremiumUsers"
    },
    "devs": {
        "resource": "Devs"
    }
}

def build_allowed_resource(method_arn, resource_pattern):
    arn_parts = method_arn.split(":")
    region = arn_parts[3]
    account_id = arn_parts[4]
    api_gateway_arn_parts = arn_parts[5].split("/")
    api_id = api_gateway_arn_parts[0]
    stage = api_gateway_arn_parts[1]
    http_method = api_gateway_arn_parts[2]
    
    resource_base = f"arn:aws:execute-api:{region}:{account_id}:{api_id}/{stage}/{http_method}"
    clean_resource_pattern = resource_pattern.rstrip("/*")
    return [f"{resource_base}/{clean_resource_pattern}"]

def lambda_handler(event, context):
    token = event.get("authorizationToken")
    method_arn = event["methodArn"]

    if token not in user_type_config:
        return generate_policy(
            principal_id="unknown",
            effect="Deny",
            resources=method_arn,
            context={
                "token": token or "None",
                "methodArn": method_arn
            }
        )

    config = user_type_config[token]
    allowed_resources = build_allowed_resource(method_arn, config["resource"])

    return generate_policy(
        principal_id=token,
        effect="Allow",
        resources=allowed_resources,
        context={
            "token": token,
            "methodArn": method_arn
        }
    )
```

1. **generate_policy** function  
- Creates the policy document required by API Gateway.  
- **principalId**: user identifier (can be the token or string `"unknown"`).  
- **effect**: `"Allow"` or `"Deny"`.  
- **resources**: list of ARNs (API endpoints) the user can access.  
- **context**: metadata returned to API Gateway (can be forwarded to the Lambda backend).  


```python
def generate_policy(principal_id, effect, resources, context=None):
    if not isinstance(resources, list):
        resources = [resources]

    policy = {
        "principalId": principal_id,
        "policyDocument": {
            "Version": "2012-10-17",
            "Statement": [{
                "Action": "execute-api:Invoke",
                "Effect": effect,
                "Resource": resources
            }]
        }
    }
    if context:
        policy["context"] = context
    return policy
```

2. Configure **user_type_config**  
- Maps tokens → allowed resource paths.  
- Example: if the client sends token `"normalUser"` → can only call `/Users`. 

```python
user_type_config = {
    "normalUser": {
        "resource": "Users"
    },
    "premiumUser": {
        "resource": "PremiumUsers"
    },
    "devs": {
        "resource": "Devs"
    }
}
```

3. **build_allowed_resource** function  
- Parses the **methodArn** (sent by API Gateway).  
- Extracts: region, account_id, api_id, stage, http_method.  
- Rebuilds the ARN for the resource that the user is allowed to access.  
- Returns the list of valid ARNs.  

```python
def build_allowed_resource(method_arn, resource_pattern):
    arn_parts = method_arn.split(":")
    region = arn_parts[3]
    account_id = arn_parts[4]
    api_gateway_arn_parts = arn_parts[5].split("/")
    api_id = api_gateway_arn_parts[0]
    stage = api_gateway_arn_parts[1]
    http_method = api_gateway_arn_parts[2]
    
    resource_base = f"arn:aws:execute-api:{region}:{account_id}:{api_id}/{stage}/{http_method}"
    clean_resource_pattern = resource_pattern.rstrip("/*")
    return [f"{resource_base}/{clean_resource_pattern}"]
```

4. **lambda_handler** function  
- Retrieves the token from the event (sent in the Authorization header).  
- If the **token does not exist** in `user_type_config` → return **Deny policy**.  
- If the **token is valid** → get resource config from `user_type_config` → build allowed resource ARN → return **Allow policy**, granting access only to the correct resource.  

```python
def lambda_handler(event, context):
    token = event.get("authorizationToken")
    method_arn = event["methodArn"]

    if token not in user_type_config:
        return generate_policy(
            principal_id="unknown",
            effect="Deny",
            resources=method_arn,
            context={
                "token": token or "None",
                "methodArn": method_arn
            }
        )

    config = user_type_config[token]
    allowed_resources = build_allowed_resource(method_arn, config["resource"])

    return generate_policy(
        principal_id=token,
        effect="Allow",
        resources=allowed_resources,
        context={
            "token": token,
            "methodArn": method_arn
        }
    )
```

## Integrate with API

- Open the API you created in [API Gateway](https://console.aws.amazon.com/apigateway/main)  
- Click **Authorizers** in the left-hand menu  
- Click **Create authorizer**

![Lambda Authorizer](/images/4/0002.png?featherlight=false&width=90pc)

- In the **Create authorizer** interface:  
  - Enter the **Authorizer name**  
  - Select **Authorizer type** as **Lambda**  
  - Choose the **Lambda function** you just created  
  - Select **Lambda event payload** as **Token**  
  - Set **Token source** to **Authorization**  
  - Disable cache to avoid unnecessary costs  
  - Click **Create authorizer**

{{% notice info %}}  
**Token source should be set to `Authorization`**, since it is a default header in CORS.  
{{% /notice %}}

![Lambda Authorizer](/images/4/0003.png?featherlight=false&width=90pc)

- You can test the Authorizer directly in its console interface.  
- Enter a Token value in **Token Value** and click **Test authorizer**  

![Lambda Authorizer](/images/4/0004.png?featherlight=false&width=90pc)  

![Lambda Authorizer](/images/4/0005.png?featherlight=false&width=90pc)  

#### Set Authorizer for Methods

- In the **Method request** tab of each Method in the API, click **Edit**  

![Lambda Authorizer](/images/4/0006.png?featherlight=false&width=90pc)

- Select **Authorization** as the Authorizer you just created  
- Click **Save**

![Lambda Authorizer](/images/4/0007.png?featherlight=false&width=90pc)

- Repeat the same steps for the other Methods.


