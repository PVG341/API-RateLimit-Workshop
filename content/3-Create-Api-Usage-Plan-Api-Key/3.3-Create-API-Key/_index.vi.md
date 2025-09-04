---
title : "Tạo API Key"
date :  "2025-09-03" 
weight : 3
chapter : false
pre : " <b> 3.3 </b> "
---

**Nội dung:**
- [Tạo API Key](#tạo-api-key)
- [Gán API Key vào Usage Plan](#gán-api-key-vào-usage-plan)
- [Thêm API Key cho Method](#thêm-api-key-cho-method)
## Tạo API Key

- Truy cập vào [API Keys](https://console.aws.amazon.com/apigateway/main/api-keys) ở Menu bên trái trong giao diện API Gateway
- Ấn vào **Create API Key**

![API Key](/images/3/00024.png?featherlight=false&width=90pc)

- Trong giao diện **Create API Key**, đặt tên cho API Key trong trường **Name**
- Chọn **Auto-generate**
- Ấn **Save**

![API Key](/images/3/00025.png?featherlight=false&width=90pc)

- Tạo thêm 2 API Key cho User và Premium user: normalUserKey, premiumUser

## Gán API Key vào Usage Plan

- Ấn vào một usage plan, chọn **Associated API Keys**
- Ấn vào **Add API Key**

![API Key](/images/3/00026.png?featherlight=false&width=90pc)

- Trong **giao diện Add API Key**, chọn **Add existing key** và chọn các API Key tương ứng với usage plan của bạn.

![API Key](/images/3/00027.png?featherlight=false&width=90pc)

- Làm tương tự với các Usage plan còn lại

## Thêm API Key cho Method

- Ấn chọn tab **Method request** của method GET trong Menu Resources
- Ấn vào **Edit**

![API Key](/images/3/00028.png?featherlight=false&width=90pc)

- Trong **giao diện Edit method request** bật **API Key required**
- Ấn **Save**

![API Key](/images/3/00029.png?featherlight=false&width=90pc)

- Làm tương tự với các method GET còn lại