---
title : "Tạo API"
date :  "2025-09-03" 
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---

**Nội dung:**
- [Tạo API](#tạo-api)
- [Tạo Resource cho API](#tạo-resource-cho-api)
- [Tạo Method cho Resource](#tạo-method-cho-resource)
- [Tạo Stage cho API](#tạo-stage-cho-api)
- [Kích hoạt Canary cho Stage](#kích-hoạt-canary-cho-stage)

## Tạo API

Truy cập vào [API Gateway](https://console.aws.amazon.com/apigateway/main)

- Ấn vào **Create API**

![API Gateway](/images/3/0001.png?featherlight=false&width=90pc)

- Chọn **Build** ở mục **REST API**

![API Gateway](/images/3/0002.png?featherlight=false&width=90pc)

- Chọn **New API**
- Đặt tên cho API ở mục **API name**
- Chọn **API endpoint type** là **Regional**
- Chọn **IP address type** là IPv4
- Ấn **Create API**

![API Gateway](/images/3/0003.png?featherlight=false&width=90pc)

## Tạo Resource cho API

**Resource trong API Gateway** chính là **“đường dẫn” (path segment)** trong REST API. Nó được kết hợp với HTTP method để tạo thành một endpoint đầy đủ mà client có thể gọi

- Ở giao diện của API vừa tạo, ấn vào **Create resource**

![API Gateway](/images/3/0004.png?featherlight=false&width=90pc)

- Đặt tên cho Resource ở mục **Resource name**
- Bật **CORS**
- Ấn **Create resource**

{{% notice info %}}
Khi bật CORS thì Method **OPTIONS** sẽ được khởi tạo cho **Resource**
{{% /notice %}}

![API Gateway](/images/3/0005.png?featherlight=false&width=90pc)

- Tạo các **Resource**: Devs, PremiumUsers, Users (Bật CORS cho các resource đó)

## Tạo Method cho Resource

- Ấn vào tên Resource (Ví dụ: /Devs) và ấn vào **Create method**

![Resource Method](/images/3/0006.png?featherlight=false&width=90pc)

- Ở giao diện **Create method**, chọn **Method type** là **GET**
- **Integration type** là **Lambda function**
- Chọn các **Lambda Funtion** tương ứng với các **Resource** (Ví dụ: chọn Lambda **devFuntion** cho **Resource /Devs** )
- Ấn vào **Create method**

![Resource Method](/images/3/0007.png?featherlight=false&width=90pc)

- Tạo tiếp các **Method** tương tự cho các **Resource** còn lại

![Resource Method](/images/3/0008.png?featherlight=false&width=90pc)

## Tạo Stage cho API

**Stage** trong **API Gateway** là **môi trường triển khai (deployment environment)** của một API, có cấu hình, URL endpoint và stage variables riêng, thường được dùng để phân biệt dev/test/prod (ví dụ với stage là prod: https://abc123.execute-api.ap-southeast-1.amazonaws.com/prod).

{{% notice info %}}
Bạn có thể tạo Stage trong giao diện **Resources** hoặc **Stages** của API
{{% /notice %}}

### Tạo Stage trong giao diện Resources

- Ấn vào **Deploy API** trong giao diện Resources

![API Stage](/images/3/0009.png?featherlight=false&width=90pc)

- Ở **Menu** vừa hiện lên, chọn phần **New stage** trong mục **Stage**
- Nhập tên cho Stage ở mục **Stage name** (mình sẽ đặt là **test**)
- Ấn vào **Deploy**

![API Stage](/images/3/00010.png?featherlight=false&width=90pc)

### Tạo Stage trong giao diện Stages

- Ấn vào **Create stage** trong giao diện **Stages**

![API Stage](/images/3/00012.png?featherlight=false&width=90pc)

- Trong **giao diện Create stage**, đặt tên cho Stage ở **Stage name**
- Chọn khung thời gian Deployment ở **Deployment**
- Trong **Additional settings**, bật **Throttling** và điều chỉnh **Rate** và **Burst** theo mong muốn
- Ấn **Create stage**

![API Stage](/images/3/00013.png?featherlight=false&width=90pc)

- Sau khi **Deploy** thì chúng ta có thể sử dụng API thông qua **Invoke URL** của các method

![API Stage](/images/3/00011.png?featherlight=false&width=90pc)

### Kích hoạt Canary cho Stage

Canary trong stage của AWS API Gateway là một cơ chế giúp bạn triển khai canary release – tức là phát hành thử một phiên bản mới của API cho một phần nhỏ lưu lượng người dùng, trước khi nhân rộng cho toàn bộ.

- Trong giao diện của stage, ấn vào **Canary**
- Ấn vào **Create Canary**

![Stage Canary](/images/3/00014.png?featherlight=false&width=90pc)

- Trong giao diện **Create Canary**, bạn có thể tùy chọn các thông số cho Canary như **Request distribution** hoặc **Stage cache** (Mình sẽ không sử dụng cache cho canary trong workshop này)
- Ấn **Create canary**

![Stage Canary](/images/3/00015.png?featherlight=false&width=90pc)

{{% notice note %}}
**Promote Canary** trước khi sử dụng để request được đi qua phiên bản mới của Stage (đề phòng bạn quên và hỏi tại sao đã thay đổi dữ liệu nhưng API vẫn như cũ)
{{% /notice %}}

![Stage Canary](/images/3/00016.png?featherlight=false&width=90pc)

{{% notice info %}}
**Request distribution** trong canary của API Gateway chính là cách mà lưu lượng request của client được phân chia giữa: **Production stage** (phiên bản hiện tại, ổn định) và **Canary stage** (phiên bản mới, đang thử nghiệm).
{{% /notice %}}


