---
title : "Tạo EventBridge Schedule"
date :  "2025-09-03" 
weight : 2
chapter : false
pre : " <b> 5.2 </b> "
---

**Nội dung:**
- [Tạo Lambda Function RateLimitCanary](5.1-Create-RateLimitCanary-Function/)
- [Tạo EventBridge Schedule](5.2-Create-EventBridge-Schedule/)

## Tạo Schedule

- Truy cập dịch vụ [Schedule của EventBridge](https://console.aws.amazon.com/scheduler/)
- Ấn **Create schedule**

![Schedule](/images/6/0009.png?featherlight=false&width=90pc)

- Trong **Step 1** của giao diện **Create schedule**, bạn có thể cài đặt tùy ý, mình sẽ cài đặt như sau:

![Schedule](/images/6/0008.png?featherlight=false&width=90pc)
![Schedule](/images/6/0007.png?featherlight=false&width=90pc)
![Schedule](/images/6/0006.png?featherlight=false&width=90pc)

- Trong **Step 2** của giao diện **Create schedule**: chọn **Target API** là **AWS Lambda** -> chọn Lambda Function RateLimitCanary -> Dán Payload:

```json
{ "API_ID":"jbjfqykyob", "STAGE_NAME":"test", "RATE_LIMIT":100, "BURST_LIMIT":200 }
```

- Payload này sẽ được gửi đến RateLimitCanary và các giá trị sẽ được áp dụng cho stage được ghi ở trong Payload.

![Schedule](/images/6/0005.png?featherlight=false&width=90pc)
![Schedule](/images/6/0004.png?featherlight=false&width=90pc)


- Trong **Step 3** của giao diện **Create schedule**:

![Schedule](/images/6/0003.png?featherlight=false&width=90pc)
![Schedule](/images/6/0002.png?featherlight=false&width=90pc)

- Trong **Step 4** của giao diện **Create schedule**: review lại và ấn **Create schedule**

![Schedule](/images/6/0001.png?featherlight=false&width=90pc)

Tương tự bạn có thể tạo các Schedule cho những thời điểm khác nhau nhằm tăng, giảm lưu lượng request của stage API.

- Khi Schedule kích hoạt thì thông báo sẽ được gửi qua Slack

![Schedule](/images/6/00010.png?featherlight=false&width=90pc)