---
title : "Tạo Authorizer"
date :  "2025-09-03" 
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

**Nội dung:**
- [Tạo Lambda Function Authorizer](#tạo-lambda-function-authorizer)
- [Tích hợp vào API](#tích-hợp-vào-api)
- [Thiết lập Authorizer cho các Method](#thiết-lập-authorizer-cho-các-method)

**Authorizer** trong API Gateway là cơ chế giúp bạn kiểm soát việc **xác thực** và **phân quyền request (dựa trên token hoặc policy)** trước khi request được chuyển đến backend.

## Tạo Lambda Function Authorizer

- Truy cập dịch vụ [Lambda](https://console.aws.amazon.com/lambda/)
- Ấn vào **Create function**

![Lambda Authorizer](/images/2/0006.png?featherlight=false&width=90pc)

-Trong giao diện **Create function**
- Chọn **Author from scratch**
- Ở mục **Function name** nhập tên của funtion (Authorizer)
- Chọn Runtime **Python 3.13**
- Chọn Architecture x86_64
- Ấn **Create function**

![Lambda Authorizer](/images/4/0001.png?featherlight=false&width=90pc)

- Trong giao diện code của Lambda Authorizer
- Lambda Authorizer này sẽ kiểm tra authorizationToken (gửi từ client), dựa vào token, nó quyết định Allow hay Deny quyền gọi API, Nếu được Allow, nó chỉ cho phép truy cập đến một số resource cụ thể (endpoint).
- Thay đổi đoạn code như sau:

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

1. Hàm **generate_policy** sẽ tạo các policy mà API Gateway cần:
- Tạo một **IAM Policy document** (theo chuẩn JSON của AWS).
- **principalId**: định danh user (ở đây chính là token hoặc chuỗi "unknown").
- **effect**: "Allow" hoặc "Deny".
- **resources**: danh sách ARN (API endpoint) được phép gọi.
- **context**: thêm metadata (trả về cho API Gateway → có thể forward đến Lambda backend)

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

2. Cấu hình **user_type_config**
- Mapping token → resource path được phép truy cập
- Ví dụ: Nếu client gửi token "normalUser" → chỉ được gọi resource /Users

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

3. Hàm **build_allowed_resource**

- **Phân tích methodArn** (ARN mà API Gateway gửi đến Authorizer)
- Lấy ra: region, account_id, api_id, stage, http_method
- Sau đó **build lại ARN mới** cho resource mà **user được phép truy cập**
- Trả về danh sách ARN hợp lệ

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

4. Hàm **lambda_handler**

- **Lấy token** từ event (client gửi trong header Authorization).
- Nếu **token không tồn tại** trong user_type_config -> **Trả về policy Deny** (cấm truy cập).
- Nếu **token hợp lệ** -> **Lấy config resource** từ user_type_config ->Xây dựng ARN resource cho phép ->**Trả về policy Allow**, **chỉ cho phép truy cập đúng resource đó**

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

## Tích hợp vào API

- Truy cập vào API vừa tạo trong [API Gateway](https://console.aws.amazon.com/apigateway/main)
- Ấn vào **Authorizer** ở Menu bên trái
- Ấn vào **Create authorizer**

![Lambda Authorizer](/images/4/0002.png?featherlight=false&width=90pc)

- Trong **giao diện Create authorizer**, đặt tên cho Authorizer ở trường **Authorizer name**
- Chọn **Authorizer type** là **Lambda**
- Chọn **Lambda function** là **Lambda function Authorizer vừa tạo**
- Chọn **Lambda event payload** là **Token**
- Đặt **Token source** là **Authorization**
- Tắt cache để đỡ tốn chi phí
- Ấn **Create authorizer**

{{% notice info %}}
**Nên đặt Token source là Authorization** vì đây là một **header mặc định trong CORS**
{{% /notice %}}

![Lambda Authorizer](/images/4/0003.png?featherlight=false&width=90pc)

- Chúng ta có thể test Authorizer vừa mới tạo trong giao diện của authorizer đó
- Nhập giá trị Token vào **Token Value** và ấn **Test authorizer**

![Lambda Authorizer](/images/4/0004.png?featherlight=false&width=90pc)

![Lambda Authorizer](/images/4/0005.png?featherlight=false&width=90pc)

#### Thiết lập Authorizer cho các Method

- Ấn vào **Edit** trong tab **Method request** của các Method trong API

![Lambda Authorizer](/images/4/0006.png?featherlight=false&width=90pc)

- Chọn **Authorization** là **Authorizer** vừa tạo
- Ấn **Save**

![Lambda Authorizer](/images/4/0007.png?featherlight=false&width=90pc)

- Thực hiện tương tự cho các Method còn lại


