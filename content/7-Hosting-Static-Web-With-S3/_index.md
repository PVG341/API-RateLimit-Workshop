---
title : "Hosting Static Website with S3 Bucket"
date :  "2025-09-03" 
weight : 7
chapter : false
pre : " <b> 7. </b> "
---

**Contents:**
1. [Create web resources](#create-web-resources)
2. [Create S3 bucket and hosting](#create-s3-bucket-and-hosting)

## Create web resources

- Create an HTML file

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
  <!--  Replace with your token which was used in Authorizer -->
  <button onclick="setUser('normalUser')">Normal User</button>
  <button onclick="setUser('premiumUser')">Premium User</button>
  <button onclick="setUser('devs')">Developer</button>

  <h2>Chọn API Key</h2>
  <!--  Replace with your API Keys -->
  <button onclick="setApiKey('ZIqUUal5OIaV6ldWfPU4WaFfWMnCDoAQ5kES3MCU')">Normal User</button>
  <button onclick="setApiKey('jYpAYtUYQA3BNUknsLaaD4iovwvKgOBJ4XyD5Cd2')">Premium User</button>
  <button onclick="setApiKey('0H8E24Q6dtacxEBGtK5p87p7c2fjX0cheH0Shmv8')">Developer</button>

  <h2>Chọn Route</h2>
  <!-- Replace with your Resource -->
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
    // Replace with your URL
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

- Create a CSS file (optional)

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

## Create S3 bucket and hosting

- Go to [S3](https://console.aws.amazon.com/s3/home)
- Click **Create bucket**

![S3 bucket](/images/8/0001.png?featherlight=false&width=90pc)

- Enter a name in **Bucket name** and click **Create bucket**

![S3 bucket](/images/8/0002.png?featherlight=false&width=90pc)

1. Add files to the bucket
- Open the newly created bucket and click **Upload**

![S3 bucket](/images/8/0003.png?featherlight=false&width=90pc)

- Click **Add files** to upload the required files  
- Click **Upload**

![S3 bucket](/images/8/0004.png?featherlight=false&width=90pc)

2. Enable Static website hosting

- Go to the bucket’s **Properties** tab

![S3 bucket](/images/8/0005.png?featherlight=false&width=90pc)

- Find and enable **Static website hosting**
- Select **Hosting type** as **Host a static website**
- Set **Index document** to your `index.html` file

![S3 bucket](/images/8/0006.png?featherlight=false&width=90pc)

3. Disable public access block
- Go to the **Permissions** tab and turn off **Block public access (bucket settings)**

![S3 bucket](/images/8/0007.png?featherlight=false&width=90pc)

4. Add a bucket policy
- Go to **Permissions** → **Bucket policy** and paste your policy (replace **api-rate-limit-web/** with your bucket name):

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