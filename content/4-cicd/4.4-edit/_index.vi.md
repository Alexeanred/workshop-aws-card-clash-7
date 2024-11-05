---
title : "Edit pipeline"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 4.4 </b> "
---
* Sau khi tạo pipeline xong, vào edit build stage:
    * Thêm Output artifacts và sửa lại S3_BUCKET_NAME.
    * Thêm 3 output artifacts: addItemArtifact, removeItemArtifact, layerArtifact.
    * S3_BUCKET_NAME sửa tên bucket ví dụ: ```codepipeline-us-east-1-534781109788```.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.16.png) 
* Edit lại deploy stage:
    * Xóa Input Artifact cũ là BuildArtifact, thêm vào 3 aritfacts mới tạo ở output của build stage.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.17.png) 




