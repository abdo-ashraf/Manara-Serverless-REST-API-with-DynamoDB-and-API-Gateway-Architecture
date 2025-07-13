# Manara - Serverless REST API with DynamoDB and API Gateway Architecture

This repository contains the final project for the **Manara AWS Solutions Architect Associate** course.

---

## Solution Overview

This project delivers a fully serverless REST API on AWS, using **Amazon API Gateway** to expose endpoints, **AWS Lambda** to handle CRUD operations, and **Amazon DynamoDB** for data storage. The solution ensures high scalability, availability, and zero server maintenance. A static front-end hosted on **Amazon S3** is distributed via **Amazon CloudFront** for low-latency global access, while **IAM roles** enforce secure access control and **Amazon CloudWatch** provides logging and monitoring. This architecture follows AWS best practices for building cost-effective, event-driven applications with minimal operational overhead.

---

## Architecture Diagram

![Architecture Diagram](./Serverless%20RestAPI.jpg)

---

## Architecture Breakdown

1. **Users**  
   End-users interact with the application, initiating requests either to retrieve the static frontend through their web browsers or to perform API operations via HTTP calls.

2. **AWS Route 53**  
   Serves as the authoritative DNS service. It resolves custom domain names for both the static frontend (pointing to CloudFront) and the REST API's custom domain (pointing to API Gateway), directing user traffic to the appropriate service.

3. **AWS Certificate Manager (ACM)**  
   Provides and manages SSL/TLS certificates for the custom domains. These certificates are crucial for establishing secure HTTPS connections for both the CloudFront distribution (serving the static frontend) and the API Gateway (for the REST API endpoints).

4. **Amazon CloudFront**  
   Operates as a global Content Delivery Network (CDN). It caches and delivers the static frontend assets (HTML, CSS, JavaScript) from the S3 bucket to users with low latency. CloudFront utilizes the ACM certificate to ensure secure HTTPS delivery of the frontend content.  
   - `GET/: static_frontend:` This explicitly shows CloudFront fetching the static content from S3.

5. **Amazon S3**  
   Functions as the secure and highly available storage for all static frontend files that constitute the user interface of the application. It acts as the origin for the CloudFront distribution.

6. **Amazon API Gateway**  
   Acts as the highly scalable and secure "front door" for your backend Lambda functions. It exposes the RESTful endpoints (GET, POST, PUT, DELETE), handles request routing, and seamlessly integrates with the various Lambda functions.  
   - HTTPS REST: Indicates that all API communications occur over secure HTTPS connections, using the SSL/TLS certificate provided by ACM.  
   - Push logs to CloudWatch permission: API Gateway is configured with the necessary permissions to send detailed access logs and execution metrics directly to Amazon CloudWatch, aiding in monitoring and troubleshooting.

7. **AWS Lambda**  
   The core serverless compute service that executes your application's business logic. Each specific API method (GET, POST, PUT, DELETE) triggers a dedicated Lambda function:  
   - **Getitem Lambda**: Handles retrieval operations.  
   - **Putitem Lambda**: Handles creation operations.  
   - **Updateitem Lambda**: Handles modification operations.  
   - **Deleteitem Lambda**: Handles deletion operations.  
   - **Lambda Roles**: Each Lambda function (e.g., `Getitem Lambda role`) operates under a specific IAM role. These roles define the precise permissions required for the function to execute, access other AWS services (like DynamoDB), and push logs to CloudWatch.

8. **Amazon DynamoDB**  
   A fully managed, highly available NoSQL database service used for persistent storage of your application's data (e.g., to-do list items, customer records).  
   - **Policy to allow access only from your Lambda's IAM role**: Access to DynamoDB is strictly controlled by an IAM policy. This policy ensures that only the specific IAM roles associated with your Lambda functions have the necessary permissions to perform read/write operations on the DynamoDB table, reinforcing the principle of least privilege.

9. **Amazon CloudWatch**  
   The primary monitoring and observability service. It receives and stores detailed logs and metrics pushed directly from Amazon API Gateway and AWS Lambda functions, enabling real-time insights into API traffic, Lambda execution performance, errors, and overall application health.
