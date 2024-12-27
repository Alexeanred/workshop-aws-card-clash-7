---
title : "Introduction"
date : "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
* ![arch](/workshop-aws-card-clash-7/images/arc.png) 

## Architectural ideas: 
* **User will update the Lambda function code through the following process:**
    * User pushes code to GitHub repository, and CI/CD pipeline created by CodePipeline will automatically deploy.
    * CodePipeline will have 3 stages: Source, Build, Deploy.
    * The Source phase will track changes from the GitHub repository and update the latest.
    * Next, the Build phase will retrieve the source code and perform the build and test process.
    * Finally, the Deploy phase will use a CloudFormation template to deploy the changed resources.
* **Note**: Because the AWS CodeCommit service is about to be discontinued, we will replace it with Github.

## Service introduction:

**Amazon CodePipeline**: Service that helps automate continuous integration and deployment (CI/CD) with the ability to link different code sources, build pipelines, and deployment tools, helping to increase application development and deployment speed.

**Amazon CodeBuild**: Automated source code building and testing service, providing scalable build environments to meet needs, allowing easy resource configuration and handling of build processes upon request.

**Amazon S3**: Scalable and persistent object storage service, used to securely and resiliently store and retrieve data, great for archiving files source, artifact, or record during CI/CD.

**AWS Lambda**: Serverless computing service that allows code to run without server management, simplifying deployment of compact functions without infrastructure concerns.

**Amazon API Gateway**: Service that enables secure and scalable building, deployment, and management of APIs, connecting to Lambda to build RESTful APIs or WebSocket APIs.

**AWS CloudFormation**: Service that provides a way to manage and deploy infrastructure-as-code (IaC) resources, making it easy to create, update, and delete AWS resources using structured templates.

**Amazon DynamoDB**: A fully managed, low-latency NoSQL database service that is suitable for storing structured data, is easily scalable, and provides high availability.