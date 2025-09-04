---
title : "Tạo IAM Role"
date :  "2025-09-03" 
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---

**Nội dung:**
- [Tạo IAM Role](#tạo-iam-role)


## Tạo IAM Role

1. Truy cập dịch vụ [IAM](https://console.aws.amazon.com/iam/home)

- Ấn vào **Role** ở Menu bên trái

![IAM](/images/02/0001.png?featherlight=false&width=90pc)

- Ấn vào **Create Role**

![IAM](/images/02/0002.png?featherlight=false&width=90pc)

2. Trong giao diện **Create Role**

- Chọn **Trusted entity type** là AWS service
- Chọn **Use case** là Lambda
- Ấn **Next**

![IAM](/images/02/0003.png?featherlight=false&width=90pc)

- Thêm các **Policy** như sau
- Ấn **Next**

![IAM](/images/02/0004.png?featherlight=false&width=90pc)

- Ở bước 3, đặt tên cho **Role** và review lại các tùy chọn
- Ấn **Create role**

![IAM](/images/02/0005.png?featherlight=false&width=90pc)
