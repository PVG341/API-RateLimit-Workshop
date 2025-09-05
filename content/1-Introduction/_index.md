---
title : "Introduction"
date : "2025-09-03"
weight : 1
chapter : false
pre : " <b> 1. </b> "
---

## Overview

This is a workshop on API Rate Limiting and Throttling on AWS using Lambda Authorizers to control access and protect API systems. Lambda Authorizers are implemented to enforce custom rate limiting logic, allowing the application of dynamic limits based on user types. Next, the workshop covers building APIs with AWS API Gateway, setting up Resources, Methods, Usage Plans, and API Keys. It also integrates with CloudWatch and EventBridge to establish monitoring and alerting, helping detect anomalies early and trigger timely notifications.

[Project S3 Link](http://api-rate-limit-web.s3-website-ap-southeast-2.amazonaws.com/)
[Project Proposal](https://docs.google.com/document/d/1X52AMno0pRGWW17u35ubVdvPTFvYAeik/edit?usp=drive_link&ouid=116677375895548588303&rtpof=true&sd=true)


![AWS Support](/images/1/0001.png?featherlight=false&width=90pc)

**Contents:**
1. [Introduction](1-introduction/)
2. [Preparation Steps](2-preparation-steps/)
3. [Create API, Usage Plan, API Key](3-create-api-usage-plan-api-key/)
4. [Create Authorizer](4-create-authorizer/)
5. [Create EventBridge Schedule](5-create-eventBridge-schedule)
6. [Create CloudWatch Alarm](6-create-cloudwatch-alarm)
7. [Host Static Web with S3](7-hosting-static-web-with-s3)
8. [Clean Up Resources](8-clean-up)