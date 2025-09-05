---
title : "Hosting web tĩnh với S3 Bucket"
date :  "2025-09-03" 
weight : 7
chapter : false
pre : " <b> 7. </b> "
---

**Nội dung:**
1. [Tạo các tài nguyên cho web](#tạo-các-tài-nguyên-cho-web)
2. [Tạo S3 bucket và hosting](#tạo-s3-bucket-và-hosting)

## Tạo các tài nguyên cho web

- Tạo file HTML:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Rate Limit Test</title>
  <link rel="stylesheet" href="./css/index.css">
</head>
<body>
  <h2>Chọn loại người dùng:</h2>
  <!-- Sử dụng các token được thiết lập trong Lambda Authorizer -->
  <button onclick="setUser('normalUser')">Normal User</button>
  <button onclick="setUser('premiumUser')">Premium User</button>
  <button onclick="setUser('devs')">Developer</button>

  <h2>Chọn API Key</h2>
  <!-- Sử dụng API Key cho các loại người dùng của bạn -->
  <button onclick="setApiKey('ZIqUUal5OIaV6ldWfPU4WaFfWMnCDoAQ5kES3MCU')">Normal User</button>
  <button onclick="setApiKey('jYpAYtUYQA3BNUknsLaaD4iovwvKgOBJ4XyD5Cd2')">Premium User</button>
  <button onclick="setApiKey('0H8E24Q6dtacxEBGtK5p87p7c2fjX0cheH0Shmv8')">Developer</button>

  <h2>Chọn Route</h2>
  <!-- Sử dụng Resource của bạn -->
  <button onclick="setURL('Users')">Normal User</button>
  <button onclick="setURL('PremiumUsers')">Premium User</button>
  <button onclick="setURL('Devs')">Developer</button>

  <p>Current token: <span id="currentToken">none</span></p>
  <p>Current API Key: <span id="currentApiKey">none</span></p>
  <p>Current URL: <span id="currentURL">none</span></p>
  <button onclick="makeRequest()">Gửi Request</button>

  <pre id="response"></pre>

  <script>
    let currentToken = "";
    let currentApiKey = "";
    let currentPath = "";
    // Thay thế bằng URL stage của bạn
    const baseURL = "https://jbjfqykyob.execute-api.ap-southeast-2.amazonaws.com/test/";

    function setUser(token) {
      currentToken = token;
      document.getElementById("currentToken").textContent = token;
      
    }

    function setApiKey(apiKey) {
      currentApiKey = apiKey;
      document.getElementById("currentApiKey").textContent = apiKey;
    }

    function setURL(path) {
      currentPath = path;
      document.getElementById("currentURL").textContent = baseURL + path;
    }

    async function makeRequest() {
      const responseBox = document.getElementById("response");

      if (!currentToken || !currentApiKey || !currentPath) {
        alert("Hãy chọn loại người dùng trước.");
        return;
      }

      try {
        const res = await fetch(baseURL + currentPath, {
          method: "GET",
          headers: {
            "Authorization": currentToken,
            "X-Api-Key": currentApiKey,
          }
        });

        const text = await res.text();
        responseBox.textContent = `Status: ${res.status}\n\nBody:\n${text}`;
      } catch (err) {
        responseBox.textContent = `Lỗi khi gửi request:\n${err}`;
      }
    }
  </script>
</body>
</html>
```

- Tạo file CSS (tùy chọn):

```css
/* Reset default margins and padding */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

/* Body styling */
body {
  font-family: 'Arial', sans-serif;
  background-color: #f4f7fa;
  padding: 20px;
  max-width: 800px;
  margin: 0 auto;
  color: #333;
}

/* Headings */
h2 {
  font-size: 1.5rem;
  margin-bottom: 15px;
  color: #2c3e50;
}

/* Button styling */
button {
  background-color: #3498db;
  color: white;
  border: none;
  padding: 10px 20px;
  margin: 5px;
  border-radius: 5px;
  cursor: pointer;
  font-size: 1rem;
  transition: background-color 0.3s ease, transform 0.1s ease;
}

button:hover {
  background-color: #2980b9;
  transform: translateY(-2px);
}

button:active {
  transform: translateY(0);
}

/* Paragraph and span styling */
p {
  font-size: 1.1rem;
  margin: 10px 0;
}

span {
  font-weight: bold;
  color: #e74c3c;
}

/* Response area */
pre#response {
  background-color: #2c3e50;
  color: #ecf0f1;
  padding: 15px;
  border-radius: 5px;
  min-height: 100px;
  font-family: 'Courier New', monospace;
  white-space: pre-wrap;
  word-wrap: break-word;
  margin-top: 20px;
  overflow-x: auto;
}

/* Responsive design */
@media (max-width: 600px) {
  body {
    padding: 10px;
  }

  button {
    display: block;
    width: 100%;
    margin: 10px 0;
  }

  h2 {
    font-size: 1.3rem;
  }

  p {
    font-size: 1rem;
  }
}
```

## Tạo S3 bucket và hosting

- Truy cập dịch vụ [S3](https://console.aws.amazon.com/s3/home)
- Ấn vào **Create bucket**

![S3 bucket](/images/8/0001.png?featherlight=false&width=90pc)

- Đặt tên cho bucket ở **Bucket name** và ấn **Create bucket**

![S3 bucket](/images/8/0002.png?featherlight=false&width=90pc)

1. Thêm file vào bucket
- Truy cập bucket vừa tạo và ấn vào **Upload**

![S3 bucket](/images/8/0003.png?featherlight=false&width=90pc)

- Ấn vào **Add files** để thêm các file cần thiết
- Ấn **Upload**

![S3 bucket](/images/8/0004.png?featherlight=false&width=90pc)

2. Enable Static website hosting

- Vào tab **Properties** của bucket

![S3 bucket](/images/8/0005.png?featherlight=false&width=90pc)

- Tìm và Enable **Static website hosting**
- Chọn **Hosting type** là **Host a static website**
- **Index document** là file Index.html

![S3 bucket](/images/8/0006.png?featherlight=false&width=90pc)

3. Bỏ chặn public access
- Vào tab **Permissions**, tìm và tắt **Block public access (bucket settings)**

![S3 bucket](/images/8/0007.png?featherlight=false&width=90pc)

4. Thêm bucket policy
- Vào tab **Permissions** → Bucket policy và dán (thay **api-rate-limit-web/** bằng tên bucket của bạn):

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::api-rate-limit-web/*"
        }
    ]
}
```