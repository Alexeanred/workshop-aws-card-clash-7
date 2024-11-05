---
title : "Deploy Stage"
date : "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 4.3 </b> "
---
* At the deploy stage, we choose **AWS Cloudformation**.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.13.png) 
* Select temporary Input artifacts as BuildArtifact and SourceArtifact. We will change later.
* Action mode select **Create or update a stack**.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.14.png)
* Stack name: ```ci-cd-lambda-stack```.
* In the Template section, we will provide where the template is saved for this stage:
    * Select Artifact name: **SourceArtifact**.
    * Select file name: ```lambda_functions/template.yaml```. Here is the link to the template in the github repo.
* In the Capabilities section, select CAPABILITY_NAMED_IAM to create IAM resources with custom names.
* I made an error and added this Capabilities and it was ok.
* In the Role name field, we fill in the exact ARN of the role we created earlier. For example: **arn:aws:iam::471112789042:role/CloudFormationExecutionRole**.
* We scroll down the Advanced section. 
* In the Parameter overrides section:
    * Fill in the following:
```yaml
{
  "BucketName": { "Fn::GetArtifactAtt" : ["addItemArtifact", "BucketName"]},
  "AddItemObjectKey": { "Fn::GetArtifactAtt" : ["addItemArtifact", "ObjectKey"]},
  "RemoveItemObjectKey": { "Fn::GetArtifactAtt" : ["removeItemArtifact", "ObjectKey"]},
  "LayerObjectKey": { "Fn::GetArtifactAtt" : ["layerArtifact", "ObjectKey"]}
}
```
* After configuration is complete, select the **Done** button.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.15.png) 
* Review all information, select **Create pipeline** button.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.18.png)
