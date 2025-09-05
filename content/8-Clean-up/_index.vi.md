---
title : "Dọn dẹp tài nguyên"
date :  "2025-09-03" 
weight : 8
chapter : false
pre : " <b> 8. </b> "
---

**Nội dung**
- [Xóa S3](#xóa-s3)
- [Xóa EventBridge Schedule](#xóa-eventbridge-schedule)
- [Xóa Cloudwatch Alarm](#xóa-cloudwatch-alarm)
- [Xóa API Gateway](#xóa-api-gateway)
- [Xóa Lambda](#xóa-lambda)
- [Xóa IAM Role](#xóa-iam-role)

Bây giờ là các bước cần tiến hành để xóa các tài nguyên đã tạo.

## Xóa S3

1. Truy cập [S3](https://console.aws.amazon.com/lambda/)
- Ấn chọn S3 bucket đã tạo
- Ấn **Empty**
- Điền **permanently delete**, sau đó ấn Empty để tiến hành xóa object trong bucket
- Ấn **Exit**

2. Sau khi xóa hết object trong bucket, ấn **Delete**
- nhập tên bucket và ấn **Delete bucket**

## Xóa EventBridge Schedule

- Truy cập dịch vụ [Schedule của EventBridge](https://console.aws.amazon.com/scheduler/)
- Chọn Schedule và ấn **Delete**
- Xác nhận **Delete**

## Xóa Cloudwatch Alarm

- Truy cập dịch vụ [Cloudwatch Alarm](https://console.aws.amazon.com/cloudwatch/home#alarmsV2)
- Chọn Alarm
- Ấn **Action** -> chọn **Delete**
- Xác nhận **Delete**

## Xóa API Gateway

1. Xóa API

- Truy cập vào [API Gateway](https://console.aws.amazon.com/apigateway/main)
- Chọn API -> Chọn **Delete**

2. Xóa Usage plan

-  Truy cập vào [Usage plans](https://console.aws.amazon.com/apigateway/main/usage-plans)
- Chọn các usage plan -> Chọn **Delete**
- Gõ **confirm** và ấn **Delete**

3. Xóa API Key

-  Truy cập vào [API Keys](https://console.aws.amazon.com/apigateway/main/api-keys)
- Chọn các API Key -> Ấn vào **Action**
- Chọn **Delete** -> Xác nhận **Delete**

## Xóa Lambda

- Truy cập [Lambda](https://console.aws.amazon.com/lambda/)
- Chọn các Lambda -> Ấn vào **Action**
- Chọn **Delete** -> gõ **confirm** -> Ấn vào **Delete**

## Xóa IAM Role

- Truy cập [IAM Role](https://console.aws.amazon.com/iam/home/roles)
- Chọn role và ấn **Delete**
- gõ tên role và ấn **Delete**