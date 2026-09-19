# Build a Three-Tier Serverless Web Application on AWS

A hands-on AWS project implementing a simple three-tier serverless web application using **Amazon S3, Amazon CloudFront, API Gateway, AWS Lambda, and Amazon DynamoDB**.

The main objective was to move beyond learning individual AWS services for the **AWS Solutions Architect Associate** certification and gain practical experience integrating multiple services into a working application.

**Author:** Kacem Lassoued
**Email:** [kacem.lass@gmail.com](mailto:kacem.lass@gmail.com)

---

## Architecture

The application follows a three-tier architecture:

```text
                         ┌─────────────────┐
                         │      User       │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │   CloudFront    │
                         │ Content Delivery│
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │       S3        │
                         │ Static Frontend │
                         └────────┬────────┘
                                  │
                              API Request
                                  │
                                  ▼
                         ┌─────────────────┐
                         │  API Gateway    │
                         │   HTTP API      │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │     Lambda      │
                         │ Backend Logic   │
                         └────────┬────────┘
                                  │
                                  ▼
                         ┌─────────────────┐
                         │    DynamoDB     │
                         │  Application DB │
                         └─────────────────┘
```

### Request Flow

1. The user accesses the application through **CloudFront**.
2. CloudFront delivers the static frontend hosted in **Amazon S3**.
3. The frontend JavaScript sends a request to **API Gateway**.
4. API Gateway invokes the **Lambda** function.
5. Lambda retrieves the requested data from **DynamoDB** using the `userId`.
6. Lambda returns the data through API Gateway.
7. The frontend displays the returned data to the user.

---

## AWS Services Used

| AWS Service            | Purpose                               |
| ---------------------- | ------------------------------------- |
| **Amazon S3**          | Hosts the static frontend             |
| **Amazon CloudFront**  | Distributes the frontend content      |
| **Amazon API Gateway** | Provides the backend HTTP API         |
| **AWS Lambda**         | Handles backend application logic     |
| **Amazon DynamoDB**    | Stores and retrieves application data |

---

# 1. Presentation Tier

The presentation tier is responsible for the application's user interface.

I used **Amazon S3** to host the static website files, including the HTML and JavaScript used by the frontend.

**CloudFront** was configured as the distribution layer in front of the application, allowing the frontend to be accessed through a CloudFront distribution.

The main responsibilities of this tier are:

* Serving the application's static content
* Providing the user interface
* Sending requests to the backend API
* Displaying the data returned by the backend

The root object for the website was configured as `index.html`.

![S3 static website configuration](http://nextwork.ai/elated_orange_swift_popokatea/uploads/aws-compute-threetier_3a4b5c6d)

---

# 2. Logic Tier

The logic tier is implemented using **API Gateway and AWS Lambda**.

API Gateway provides the HTTP endpoint used by the frontend, while Lambda contains the backend application logic.

The Lambda function receives the request and retrieves the corresponding information from DynamoDB using the supplied `userId`.

The basic flow is:

```text
API Gateway
     │
     ▼
  Lambda
     │
     ▼
 DynamoDB
```

Lambda is responsible for querying the database and constructing the response returned to the API client.

![Lambda and backend configuration](http://nextwork.ai/elated_orange_swift_popokatea/uploads/aws-compute-threetier_6a7b8c9d)

---

# 3. Data Tier

The data tier uses **Amazon DynamoDB** as the application's database.

DynamoDB was selected because the application's data access pattern is relatively simple and does not require a relational database.

The table uses:

```text
Partition Key: userId
```

The `userId` was selected as the partition key because the primary access pattern for this application is retrieving information associated with a specific user.

This is also a useful demonstration of an important DynamoDB design principle: the table's key structure should be designed around the application's expected access patterns.

![DynamoDB table configuration](http://nextwork.ai/elated_orange_swift_popokatea/uploads/aws-compute-threetier_u1v2w3x4)

---

# 4. Connecting the Application Layers

Once the presentation, logic, and data tiers were configured, the next step was connecting them into a functional application.

The frontend JavaScript communicates with the API Gateway endpoint. API Gateway then invokes Lambda, which retrieves the appropriate record from DynamoDB.

The resulting architecture is:

```text
CloudFront
     │
     ▼
    S3
     │
     │ API request
     ▼
API Gateway
     │
     ▼
  Lambda
     │
     ▼
DynamoDB
```

Getting the individual services working was only part of the exercise. The more useful part was troubleshooting the interactions between them.

After making the necessary changes, the API successfully returned the expected data.

![Working API response](http://nextwork.ai/elated_orange_swift_popokatea/uploads/aws-compute-threetier_a112c3d5)

---

# 5. Troubleshooting

The application initially encountered several issues while connecting the different tiers.

This was an important part of the project because the AWS services can work correctly individually while the complete application still fails due to configuration or integration issues.

## Missing API Gateway URL

The first issue was caused by the frontend JavaScript not containing the correct API Gateway endpoint.

The browser therefore had no valid backend endpoint to call.

I updated `script.js` with the API Gateway URL and uploaded the updated JavaScript file to the S3 bucket.

After this change, the frontend could reach the API, but the browser then exposed another issue.

---

## CORS Error

The next problem was a **Cross-Origin Resource Sharing (CORS)** error.

The frontend was being served from the CloudFront origin while the API was exposed through API Gateway. Because these are different origins, the browser's same-origin security policy required the API to explicitly allow the frontend to make the request.

The browser console showed the resulting CORS issue.

![CORS error in browser console](http://nextwork.ai/elated_orange_swift_popokatea/uploads/aws-compute-threetier_a1b2c3d5)

---

# 6. Resolving CORS

To resolve the issue, I configured the API to allow the frontend origin to make the required `GET` request.

I also modified the Lambda response to include the appropriate CORS response headers.

This was an important practical lesson: configuring CORS is not simply about making an API endpoint publicly accessible. The browser also evaluates whether the response permits the requesting origin.

The relevant response behavior included a header such as:

```http
Access-Control-Allow-Origin: <allowed-origin>
```

After updating the API configuration and Lambda response, the browser was able to complete the request successfully.

![CORS configuration](http://nextwork.ai/elated_orange_swift_popokatea/uploads/aws-compute-threetier_1qthryj2)

---

# 7. Final Result

After resolving the API endpoint and CORS configuration issues, the complete application worked as expected.

A request could successfully travel through the different layers:

```text
User
 │
 ▼
CloudFront
 │
 ▼
S3
 │
 │ API request
 ▼
API Gateway
 │
 ▼
Lambda
 │
 ▼
DynamoDB
 │
 ▼
Lambda
 │
 ▼
API Gateway
 │
 ▼
Frontend
```

The final application successfully retrieved and displayed the expected data.

![Final working application](http://nextwork.ai/elated_orange_swift_popokatea/uploads/aws-compute-threetier_2b3c4d5e)

---

# Key Concepts Learned

Although this is a relatively small application, it provided hands-on experience with several important AWS concepts.

### Three-tier architecture

The project demonstrated how an application can be separated into distinct presentation, logic, and data layers.

### Serverless architecture

The application uses managed AWS services rather than maintaining traditional application servers.

Lambda provides compute, DynamoDB provides data storage, and API Gateway provides the HTTP interface.

### AWS service integration

The main learning objective was understanding how individual AWS services can be combined into a functioning application.

Studying S3, Lambda, API Gateway, DynamoDB, and CloudFront independently is different from understanding how they interact as part of a complete request path.

### DynamoDB access patterns

Using `userId` as the partition key reinforced the importance of designing DynamoDB tables around expected access patterns.

### API Gateway and Lambda

The project provided practical experience with routing HTTP requests through API Gateway and processing them using Lambda.

### CORS

Troubleshooting the application provided practical experience with browser-enforced cross-origin restrictions and configuring the API to allow requests from the frontend.

### Lambda HTTP responses

I also gained experience handling HTTP response headers from Lambda, including the headers required for CORS.

---

# Troubleshooting Summary

| Problem                              | Cause                                              | Resolution                                                    |
| ------------------------------------ | -------------------------------------------------- | ------------------------------------------------------------- |
| Frontend could not reach the backend | API Gateway URL was missing from `script.js`       | Added the correct API endpoint and re-uploaded the file to S3 |
| Browser blocked the API request      | CORS was not correctly configured                  | Configured CORS for the frontend origin                       |
| CORS response was incomplete         | Lambda was not returning the required CORS headers | Added the appropriate response headers to Lambda              |

---

# Project Reflection

The main purpose of this project was to gain practical experience connecting AWS services together rather than simply learning what each service does in isolation.

While preparing for the **AWS Solutions Architect Associate** certification, I had studied services such as S3, Lambda, API Gateway, DynamoDB, and CloudFront individually. Building this application gave me an opportunity to apply those concepts in an actual architecture.

The most valuable part of the project was the troubleshooting. The missing API endpoint and subsequent CORS issue demonstrated that knowing how an AWS service works individually is different from understanding the integration points between multiple services.

This project therefore helped bridge the gap between **AWS theory and practical implementation**.

---

# Potential Improvements

This implementation was intentionally kept small to focus on understanding the architecture and service integration.

A more production-oriented version could be extended with:

* **Terraform or AWS CDK** for Infrastructure as Code
* **CloudFront Origin Access Control (OAC)** for a more secure S3 origin
* Authentication and authorization
* More restrictive IAM permissions
* CloudWatch logging and monitoring
* API request validation
* Better application-level error handling
* Additional DynamoDB access patterns and indexes
* CI/CD for automated deployment

These improvements would turn the learning-oriented implementation into a more production-focused serverless architecture.
