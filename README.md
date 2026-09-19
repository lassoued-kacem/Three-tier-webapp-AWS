<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Build a Three-Tier Web App

**Project Link:** [View Project](http://nextwork.ai/projects/aws-compute-threetier)

**Author:** kacem lassoued  
**Email:** kacem.lass@gmail.com

---

## Build a Three-Tier Web App

![Image](http://nextwork.ai/elated_orange_swift_popokatea/uploads/aws-compute-threetier_2b3c4d5e)

---

## Introducing Today's Project!

In this project, I will build a three tier web app. I'm doing this project to learn orchestrating the different layers of a modern app and how they interact with each other

### Tools and concepts

Services I used were. Ş3 , dynamoDB , lmabda , API gateway and CloudFront. Key concepts I learnt include how to handle header from lambda with javascript , CORS configuring the API gateways , lambda permissions and so on.

### Project reflection

This project althouhg simple came with its own challenges and a lot of troubleshooting.

I chose this project because I needed to learn to operate services that i have been learning a lot about in theory while preparing for the SAA (mostly how to orchestrate and glue them together).

---

## Presentation tier

For the presentation tier, I will set up an S3 bucket to host my static website content.

I accessed my delivered website by setting the root object to index.html, updating the S3 policy to be adapted for hosting a static website to S3 (which is the origin of my distribution).

![Image](http://nextwork.ai/elated_orange_swift_popokatea/uploads/aws-compute-threetier_3a4b5c6d)

---

## Logic tier

For the logic tier, I will set up a lambda function to handle the bckend logic , which will sit behind an api gateway

The Lambda function retrieves data by looking for the corresponding information for the userID inside our dynamoDB table which is the partition key of our table.

![Image](http://nextwork.ai/elated_orange_swift_popokatea/uploads/aws-compute-threetier_6a7b8c9d)

---

## Data tier

For the data tier, We need an actual database to actually fetch and serve data from , the choice was DynamoDB ,as it is an excellent no SQL serverless choice.

The partition key for my DynamoDB table is the userId, i chose this as my partition key for the sole reason that we expect to query mostly by userId.

![Image](http://nextwork.ai/elated_orange_swift_popokatea/uploads/aws-compute-threetier_u1v2w3x4)

---

## Logic and Data tier

Once all three layers of my three-tier architecture are set up, the next step is to update our code in order to make calls to the other tiers of our infrastructure.

To test my API, it worked after few errors and few changes here and there but now it is showing the data that it is intended to do.

![Image](http://nextwork.ai/elated_orange_swift_popokatea/uploads/aws-compute-threetier_a112c3d5)

---

## Console Errors

The error in my distributed site was because i was missing the actual url of my api inside the JS code.

To resolve the error, I updated script.js by adding the url of my api. I then reuploaded it into S3 to use the new script.js.

I ran into a second error after updating script.js. This was an error with CORS because i have not enabled it at the api gateway level.

![Image](http://nextwork.ai/elated_orange_swift_popokatea/uploads/aws-compute-threetier_a1b2c3d5)

---

## Resolving CORS Errors

To resolve the CORS error, I enbled the GET request from the origin of my cloudfront distribution.

I also updated my Lambda function because... The changes I made were including the CORS header because my lambda function is the one hadling them.

![Image](http://nextwork.ai/elated_orange_swift_popokatea/uploads/aws-compute-threetier_1qthryj2)

---

## Fixed Solution

I verified the fixed connection between API Gateway and CloudFront by testing the final result and it yielded in what was expected to happen'

![Image](http://nextwork.ai/elated_orange_swift_popokatea/uploads/aws-compute-threetier_2b3c4d5e)

---

---
