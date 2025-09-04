---
title : "Tạo Cloudwatch Alarm và Dashboard"
date :  "2025-09-03" 
weight : 6
chapter : false
pre : " <b> 6. </b> "
---

**Nội dung:**
- [Thiết lập Cloudwatch Dashboard](#thiết-lập-cloudwatch-dashboard)
- [Tạo Cloudwatch Alarm](#thiết-lập-alarm)
- [Thêm quyền Invoke cho Alarm](#thêm-quyền-invoke-cho-alarm)
- [Kiểm thử Alarm](#kiểm-thử-alarm)

## Thiết lập Cloudwatch Dashboard

- Truy cập dịch vụ [Cloudwatch Dashboard](https://console.aws.amazon.com/cloudwatch/home#dashboards/)
- Ấn vào **Create dashboard**

![Alarm](/images/7/00016.png?featherlight=false&width=90pc)

- Nhập tên cho Dashboard ở trường **Dashboard name**
- Ấn **Create dashboard**

![Alarm](/images/7/00017.png?featherlight=false&width=90pc)

- Trong giao diện của dashboard vừa tạo, ấn vào icon dấu **+** ở góc trên bên phải

![Alarm](/images/7/0001.png?featherlight=false&width=90pc)

- Trong giao diện **Add widget**, chọn **Data source types** là **Cloudwatch**
- Chọn **Metrics** là **Line**
- Ấn **Next**

![Alarm](/images/7/0002.png?featherlight=false&width=90pc)

- Trong giao diện **Add metric graph** chọn ApiGateway -> By Stage -> chọn các metric bạn muốn
- Ấn **Create widget**

![Alarm](/images/7/0003.png?featherlight=false&width=90pc)
![Alarm](/images/7/0004.png?featherlight=false&width=90pc)
![Alarm](/images/7/0005.png?featherlight=false&width=90pc)

- Nếu đã hài lòng với dashboard của mình, chọn **Save**

![Alarm](/images/7/0006.png?featherlight=false&width=90pc)

## Thiết lập Alarm

- Truy cập dịch vụ [Cloudwatch Alarm](https://console.aws.amazon.com/cloudwatch/home#alarmsV2)
- Ấn vào **Create Alarm**

![Alarm](/images/7/00018.png?featherlight=false&width=90pc)

- Trong **Step 1** của giao diện **Create alarm** chọn **Select metric** -> chọn ApiGateway -> By Stage -> chọn Count của stage test -> Ấn **Select metric**

![Alarm](/images/7/00019.png?featherlight=false&width=90pc)
![Alarm](/images/7/0007.png?featherlight=false&width=90pc)

- Trong giao diện vừa hiện ra, chọn **Statistic** là **Sum**, **Threshold** là Static,  **Whenever Count is...** Greater **Than...** 10 (Để test function)
- Ấn **Next**

![Alarm](/images/7/0008.png?featherlight=false&width=90pc)

- Ở **Step 2** chọn **Alarm state trigger** là **In alarm**
- Chọn **Create new topic**, đặt tên cho topic, email của bạn và ấn **Create topic**

![Alarm](/images/7/0009.png?featherlight=false&width=90pc)

- Thêm **Lambda action** và chọn lambda ForceChangeLimit
- Ấn **Next**

![Alarm](/images/7/00010.png?featherlight=false&width=90pc)

- Ở **Step 3** đặt tên cho Alarm và ấn **Next**

![Alarm](/images/7/00011.png?featherlight=false&width=90pc)

- Ở **Step 4** Review lại và ấn **Create alarm**

![Alarm](/images/7/00020.png?featherlight=false&width=90pc)

## Thêm quyền Invoke cho Alarm

- Truy cập vào Lambda ForceChangeLimit và ấn vào tab **Configuration**

![Alarm](/images/7/00012.png?featherlight=false&width=90pc)

- Chọn **Permission** -> ở mục **Resource-based policy statements**, ấn **Add permissions**

![Alarm](/images/7/00013.png?featherlight=false&width=90pc)

- Chọn **Service** là **Other**
- Đặt **StatementID**
- Đặt **Principal** là **lambda.alarms.cloudwatch.amazonaws.com**
- **Source ARN** là ARN của Alarm vừa tạo
- **Action** chọn **lambda:InvokeFunction**
- Ấn **Save**

{{% notice info %}}
**Nếu thiếu action này thì Alarm sẽ không kích hoạt được Lambda Function**
{{% /notice %}}

![Alarm](/images/7/00014.png?featherlight=false&width=90pc)

## Kiểm thử Alarm

- Tạo các request và gửi thông qua Postman vượt quá mức truy cập cho phép và xem kết quả, nếu thông báo được gửi tới slack thành công và Alarm chuyển sang **In Alarm** thì tức là Alarm đã hoạt động bình thường.

![Alarm](/images/7/00015.png?featherlight=false&width=90pc)

