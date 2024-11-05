---
title : "Tạo Cloudformation Execution Role"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 2.3 </b> "
---

* Vào IAM roles, chọn nút **Create role**.
* ![pre](/workshop-aws-card-clash-7/images/2.prerequisite/2.3.png)
* Chọn **Custom trust policy**.
* Điền trust policy này vào:
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
* Ở mục **Add permissions**, thêm những quyền sau:
    * ```AmazonAPIGatewayInvokeFullAccess```
    * ```AmazonDynamoDBFullAccess```
    * ```AmazonS3FullAccess```
    * ```AWSLambda_FullAccess```
    * ```CloudWatchLogsFullAccess```
    * ```IAMFullAccess```
* Chọn nút **Next**.
* Role name: ```CloudFormationExecutionRole```
* ![pre](/workshop-aws-card-clash-7/images/2.prerequisite/2.5.png) 
* Sau khi tạo xong policy, ta thêm 1 inline policy:
    * Chọn **Create inline policy**.
* ![pre](/workshop-aws-card-clash-7/images/2.prerequisite/2.6.png) 
* Chọn JSON, điền nội dung:
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
* Đặt Policy name: ```CloudFormationAPIGatewayFullAccessPolicy```
* Chọn **Create policy**.
* ![pre](/workshop-aws-card-clash-7/images/2.prerequisite/2.8.png) 