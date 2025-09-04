---
title : "Giới thiệu"
date :  "2025-09-03" 
weight : 1
chapter : false
pre : " <b> 1. </b> "
---


## Tổng quan

Đây là workshop về API Rate Limiting và Throttling trên AWS sử dụng Lambda Authorizers để kiểm soát truy cập và bảo vệ hệ thống API. Trong đó, Lambda Authorizers được xây dựng nhằm thực thi custom rate limiting logic, cho phép áp dụng giới hạn động (dynamic limits) dựa trên loại người dùng. Tiếp theo, Xây dựng API dựa vào AWS API Gateway, thiết lập Resource, Method, Usage Plan và API Key. Tích hợp với CloudWatch, EventBridge để thiết lập monitoring và alerting, giúp phát hiện sớm các bất thường và đưa ra cảnh báo kịp thời.

[Project Test Link](https://aws.amazon.com/support/)

![AWS Support](/images/1/0001.png?featherlight=false&width=90pc)

**Nội dung:**
1. [Giới thiệu](1-introduction/)
2. [Các bước chuẩn bị](2-preparation-steps/)
3. [Tạo API, Usage Plan, API Key](3-create-api-usage-plan-api-key/)
4. [Tạo Authorizer](4-create-authorizer/)
5. [Tạo EventBridge Schedule](5-create-eventBridge-schedule)
6. [Tạo CloudWatch Alarm](6-create-cloudwatch-alarm)
7. [Host Static Web bằng S3](7-hosting-static-web-with-s3)
8. [Dọn dẹp tài nguyên](8-clean-up)