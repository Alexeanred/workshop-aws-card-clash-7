---
title : "Create Cloudformation execution role"
date : "`r Sys.Date()`" 
weight: 3
chapter: wrong
previous : " <b> 2.3 </b> "
---

* Go to IAM role, select **Create role** button.
* ![pre](/workshop-aws-card-clash-7/images/2.prerequisite/2.3.png)
* Select **Custom Trust Policy**.
* Fill in this trust policy:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "cloudformation.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```
* ![pre](/workshop-aws-card-clash-7/images/2.prerequisite/2.9.png) 
* In the **Add permissions** section, add the following permissions:
    * ```AmazonAPIGatewayInvokeFullAccess```
    * ```AmazonDynamoDBFullAccess```
    * ```AmazonS3FullAccess```
    * ```AWSLambda_FullAccess```
    * ```CloudWatchLogsFullAccess```
    * ```IAMFullAccess```
* Select the **Next** button.
* Role name: ```CloudFormationExecutionRole```
* ![pre](/workshop-aws-card-clash-7/images/2.prerequisite/2.5.png) 
* After creating the policy, we add an inline policy:
    * Select **Create inline policy**.
* ![pre](/workshop-aws-card-clash-7/images/2.prerequisite/2.6.png) 
* Choose JSON, fill in the content:
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "apigateway:POST",
                "apigateway:GET",
                "apigateway:PUT",
                "apigateway:PATCH",
                "apigateway:DELETE"
            ],
            "Resource": [
                "arn:aws:apigateway:us-east-1::/restapis",
                "arn:aws:apigateway:us-east-1::/restapis/*",
                "arn:aws:apigateway:us-east-1::/restapis/*/resources/*",
                "arn:aws:apigateway:us-east-1::/restapis/*/methods/*"
            ]
        }
    ]
}
``` 
* ![pre](/workshop-aws-card-clash-7/images/2.prerequisite/2.7.png) 
* Set Policy name: ```CloudFormationAPIGatewayFullAccessPolicy```
* Select **Create policy**.
* ![pre](/workshop-aws-card-clash-7/images/2.prerequisite/2.8.png)