---
title : "Deploy Stage"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 4.3 </b> "
---
* Ở deploy stage, ta chọn **AWS Cloudformation**.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.13.png) 
* Chọn Input artifacts tạm thời là BuildArtifact và SourceArtifact. Ta sẽ thay đổi sau.
* Action mode chọn **Create or update a stack**.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.14.png) 
* Stack name: ```ci-cd-lambda-stack```.
* Ở mục Template, nơi ta sẽ cung cấp nơi template lưu cho stage này:
    * Chọn Artifact name: **SourceArtifact**.
    * Chọn file name: ```lambda_functions/template.yaml```. Đây là đường dẫn đến template ở github repo.
* Ở mục Capabilities, chọn CAPABILITY_NAMED_IAM để tạo những IAM resources với custom names.
* Mình đã bị lỗi và thêm Capabilities này vào thì ok.
* Chỗ Role name, ta điền chính xác arn của role ta đã tạo trước đó. Ví dụ: **arn:aws:iam::471112789042:role/CloudFormationExecutionRole**.
* Ta kéo mục Advanced xuống. 
* Ở mục Parameter overrides:
    * Điền nội dung sau:
```yaml
{
  "BucketName": { "Fn::GetArtifactAtt" : ["addItemArtifact", "BucketName"]},
  "AddItemObjectKey": { "Fn::GetArtifactAtt" : ["addItemArtifact", "ObjectKey"]},
  "RemoveItemObjectKey": { "Fn::GetArtifactAtt" : ["removeItemArtifact", "ObjectKey"]},
  "LayerObjectKey": { "Fn::GetArtifactAtt" : ["layerArtifact", "ObjectKey"]}
}
```
* Sau khi cấu hình xong, chọn nút **Done**.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.15.png) 
* Review hết thông tin, chọn nút **Create pipeline**.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.18.png) 



