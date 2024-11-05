---
title : "CI/CD PipeLine"
date :  "`r Sys.Date()`" 
weight : 4 
chapter : false
pre : " <b> 4. </b> "
---
* Trên AWS management console:
  * Tìm **CodePipeline**.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.1.png) 
* Chọn **Create pipeline**:
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.2.png) 
* Chọn **Build custom pipeline**.
* Chọn **Next**.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.3.png) 
* Điền pipeline name: ```ci-cd-pipeline```
* Chọn **New service role**.
* Điền role name: có thể điền như hình.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.4.png) 
* Mở rộng phần Advanced settings, bạn sẽ thấy phần Artifact store và Encryption key.
* Trong kiến trúc của AWS card clash, bạn sẽ thấy một S3 bucket để lưu pipeline artifacts.
* Artifacts sẽ được tạo ra là output của các stage của pipeline.
* EncryptionKey là khóa mã hóa được dùng để mã hóa artifact, giúp bảo mật dữ liệu.
* CodePipeline chỉ hỗ trợ mã hóa dạng symmetric. 
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.5.png) 

 


