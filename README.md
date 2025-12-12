# **🖼️ Serverless Image Editing App using Amazon Bedrock**

A fully serverless, AI-powered image editing application built during the **AI for Bharat – Workshop 3**.
This project uses **Amazon Bedrock** for image generation, along with AWS serverless services for authentication, data storage, backend logic, API delivery, and frontend hosting.

---

## **📌 1. Problem & Solution**

### **Problem**

Modern users need fast, intuitive, and accessible image editing tools. Traditional image-editing systems require heavy infrastructure, GPU provisioning, and manual scaling—making them complex and expensive to maintain.

### **Solution**

This application provides an **AI-powered image editing experience** using natural language prompts. It is built entirely on serverless technologies, ensuring:

* Zero server management
* Automatic scaling
* Secure user authentication
* Fast and reliable backend processing
* Affordable pay-as-you-go pricing

Users can generate, edit, enhance, or transform images using **Amazon Bedrock’s Titan Image Generator G1 v2**, with all interactions handled securely through APIs.

---

## **⚙️ 2. Technical Implementation**

This project integrates the following AWS services:

### **🔹 Amazon Bedrock – AI Model**

* Uses **Titan Image Generator G1 v2**
* Performs image generation/editing via natural language prompts
* No need to manage GPUs or deploy models

### **🔹 Amazon Cognito – Authentication**

* User Pool for registration & login
* Issues JWT tokens for authorized backend access

### **🔹 DynamoDB – Database**

* Stores image generation history
* Captures prompt, metadata, and timestamps

### **🔹 AWS Lambda – Serverless Backend**

Handles:

* Reading prompt from API request
* Calling Bedrock model
* Processing image bytes
* Writing activity logs into DynamoDB
* Returning generated image to frontend

### **🔹 Amazon API Gateway – REST APIs**

* Exposes secure POST endpoints
* Integrated with Cognito for authentication
* Routes requests to Lambda

### **🔹 AWS Amplify – Frontend**

* Hosts the React web application
* Manages CI/CD
* Integrates directly with Cognito & API Gateway

---

## **📡 System Workflow**

```
User → Amplify Web App  
     → Cognito Login  
     → API Gateway (POST /generate-image)  
     → Lambda Function  
         → Calls Amazon Bedrock (Titan Image Generator)  
         → Stores metadata in DynamoDB  
     → Returns Image to Frontend  
```

---

## **🚀 3. Scaling Strategy**

### **Current Capacity**

* Lambda auto-scales per invocation
* DynamoDB supports fast read/write
* API Gateway handles request bursting
* Amplify serves global traffic efficiently

### **Future Enhancements**

* S3 for image storage
* CloudFront for global caching
* DynamoDB auto-scaling policies
* Bedrock Guardrails for content safety
* CloudWatch alerts for cost monitoring

---

## **🗂️ 4. Visual Documentation**

(Add these as images in your repo)

* Cognito User Pool Setup
<img width="1902" height="910" alt="Screenshot 2025-12-09 184908" src="https://github.com/user-attachments/assets/b34c643d-5d0c-491e-81b8-5cc171afe8fc" />

* DynamoDB Table Structure
<img width="1919" height="809" alt="Screenshot 2025-12-09 190328" src="https://github.com/user-attachments/assets/3fa5de3f-a950-4fb1-8830-659ce4ca8409" />

* Lambda Function Console
<img width="1919" height="804" alt="Screenshot 2025-12-09 193308" src="https://github.com/user-attachments/assets/69bac530-2b56-43f4-a729-66dd0a74300f" />

* API Gateway Integration
<img width="1919" height="839" alt="Screenshot 2025-12-09 202011" src="https://github.com/user-attachments/assets/299abd08-7f6d-413c-99f2-ffb7dc6a2364" />

* Amplify Deployment Screen
<img width="1919" height="758" alt="Screenshot 2025-12-09 205457" src="https://github.com/user-attachments/assets/ef52cbf4-a01c-439e-920e-c7741812a893" />

* Application UI Screenshot
<img width="1917" height="955" alt="Screenshot 2025-12-09 205731" src="https://github.com/user-attachments/assets/0f422070-5f64-45d5-a5a9-77280501ddea" />

* Architecture Diagram
<img width="1607" height="749" alt="image" src="https://github.com/user-attachments/assets/2f531c10-bd2d-4b60-a262-81b2e3381788" />

---

## **💻 5. Code Snippets**

### **Lambda Function (Sample)**

```python
import boto3
import base64
import json
from datetime import datetime

bedrock = boto3.client("bedrock-runtime")
dynamodb = boto3.resource("dynamodb")
table = dynamodb.Table("ImageGenerationRecords")

def lambda_handler(event, context):
    prompt = json.loads(event["body"])["prompt"]

    response = bedrock.invoke_model(
        modelId="amazon.titan-image-generator-v2",
        body=json.dumps({"prompt": prompt})
    )

    result = json.loads(response["body"].read())
    image_data = base64.b64decode(result["images"][0])

    table.put_item(
        Item={
            "id": str(datetime.now().timestamp()),
            "prompt": prompt,
            "timestamp": str(datetime.now())
        }
    )

    return {
        "statusCode": 200,
        "headers": {"Content-Type": "image/png"},
        "body": base64.b64encode(image_data).decode("utf-8"),
        "isBase64Encoded": True
    }
```

## **🙌 Acknowledgements**

This project was built as part of the **AI for Bharat – Hack2Skill x AWS** workshop series, focusing on hands-on learning of Amazon Bedrock and serverless architectures.

---
