---
title : "Tạo các hàm Lambda cơ bản"
date :  "2025-09-03" 
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---

**Nội dung:**
- [Tạo các hàm Lambda cơ bản](#tạo-các-hàm-lambda-phục-vụ-cho-method)


## Tạo các hàm Lambda phục vụ cho Method

1. Truy cập dịch vụ [Lambda](https://console.aws.amazon.com/lambda/)

- Ấn vào **Create function**

![AWS Lambda](/images/2/0006.png?featherlight=false&width=90pc)

2. Tạo Lambda Function **UserFunction**

2.1. Trong giao diện **Create function**

- Chọn **Author from scratch**
- Ở mục **Function name** nhập tên của funtion (UserFunction)
- Chọn Runtime **Python 3.13**
- Chọn Architecture x86_64
- Ấn **Create function**

![AWS Lambda](/images/2/0002.png?featherlight=false&width=90pc)

2.2. Trong giao diện code của Lambda Function

- Thay đổi nội dung của **json.dumb** để dễ dàng nhận biết

![AWS Lambda](/images/2/0003.png?featherlight=false&width=90pc)

3. Tạo Lambda Function **PremiumUserFunction**

- Tương tự như các bước trên

![AWS Lambda](/images/2/0004.png?featherlight=false&width=90pc)

4. Tạo Lambda Function **devFunction**

- Tương tự như các bước trên

![AWS Lambda](/images/2/0005.png?featherlight=false&width=90pc)