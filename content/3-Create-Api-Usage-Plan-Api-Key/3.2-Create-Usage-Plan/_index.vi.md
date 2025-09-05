---
title : "Tạo Usage Plan"
date :  "2025-09-03" 
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---

**Nội dung:**
- [Tạo Usage Plan](#tạo-usage-plan)
- [Thêm Associated stages](#thêm-associated-stages)

**Usage Plan** trong API Gateway là cơ chế để quản lý cách các client tiêu thụ API, bằng cách kết hợp quota (hạn mức), throttling (giới hạn tốc độ) và API Key, áp dụng cho các stage cụ thể.

## Tạo Usage Plan

- Truy cập vào [Usage Plan](https://console.aws.amazon.com/apigateway/main/usage-plan) ở Menu bên trái trong giao diện API Gateway
- Ấn vào **Create usage plan**

![Usage Plan](/images/3/00017.png?featherlight=false&width=90pc)

- Trong giao diện **Create usage plan**, nhập tên cho plan ở **Name**
- Bật **Throttling**
- Nhập **Rate** và **Burst** theo mong muốn
- Bật **Quota**
- Nhập số lượng **Requests** theo mong muốn (ví dụ cho người dùng bình thường là 100 Per day tuy nhiên mình sẽ chỉnh là 10 requests per day để dễ dàng kiểm thử trước ), và trong khoảng thời gian **Per day**
- Ấn vào **Create usage plan**
{{% notice info %}}
**Rate** là tốc độ trung bình (requests per second – RPS) mà API Gateway cho phép, **Burst** là số lượng request tối đa có thể xử lý ngay lập tức trong một thời điểm ngắn (spike).
{{% /notice %}}

![Usage Plan](/images/3/00018.png?featherlight=false&width=90pc)

- Tiếp tục tạo các **Usage plan** cho dev(rate: 1000, burst: 2000, tắt quota) và premium user(rate: 20, burst: 40, quota: 100 requests per day)

![Usage Plan](/images/3/00021.png?featherlight=false&width=90pc)

#### Thêm Associated stages

Khi một stage dược gán vào Usage Plan, tất cả các request đi qua stage đó sẽ chịu sự kiểm soát quota/throttling mà bạn định nghĩa.

- Ấn vào một trong các **Usage plan**
- Trong giao diện của **Usage plan**, ấn vào **Add stage**

![Usage Plan](/images/3/00019.png?featherlight=false&width=90pc)

- Chọn **API** là API vừa tạo (MyProjectAPI)
- Chọn Stage là **test**
- Trong phần **Method-level throttling**, chọn Resource là các Resource đã tạo trước đó, chọn Method GET, tùy chỉnh Rate và Burst
- Ấn **Add to usage plan**

![Usage Plan](/images/3/00020.png?featherlight=false&width=90pc)

- Áp dụng cho các usage plan khác

![Usage Plan](/images/3/00022.png?featherlight=false&width=90pc)
![Usage Plan](/images/3/00023.png?featherlight=false&width=90pc)