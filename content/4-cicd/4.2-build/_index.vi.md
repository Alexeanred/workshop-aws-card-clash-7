---
title : "Build Stage"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 4.2 </b> "
---
* Ở build stage, ta chọn **Other build providers**.
* Chọn **AWS CodeBuild**.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.8.png) 
* Chọn **Creating project**.
* Project name đặt tên là ```build_lambda_code```.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.9.png) 
* Các cấu hình của máy ảo codebuild dùng để build code để mặc định.\
* Chọn **New service role**.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.10.png)
* Ở buildspec, có 2 lựa chọn:
    * Ban đầu mình đã chọn **insert build commands** để ghi thẳng vào editor lun.
    * Bạn có thể chọn **Use a buildspec file** và để buildspec.yml ở github repository để codebuild lấy về và build code.

* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.11.png) 
* Chọn **Continue to CodePipeline**.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.20.png) 
* Sau khi tạo xong build project, ta có thể ghi sẵn Environemnt variables của codebuild là ```S3_BUCKET_NAME``` hoặc sau khi tạo xong ta thêm vào sau cũng được.
* Codebuild sẽ nhận input artifacts từ SourceArtifact.
* Chọn nút **Next** để qua deploy Stage.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.12.png) 
