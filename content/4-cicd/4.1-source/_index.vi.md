---
title : "Source stage"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 4.1 </b> "
---
* Ở step 3 là bước ta cấu hình stage đầu tiên là source stage.
* Kéo xuống chọn **Source provider** là GitHub (via OAuth app).
* Xác minh những thông tin cần thiết để cấp quyền cho AWS truy cập vào Github.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.6.png) 
* Sau khi đã xác minh xong, bạn chọn Repository đã tạo ở bước chuẩn bị.
* Chọn Branch ta đã dùng để push code lên github.
* Chọn Github webhooks để trigger pipeline khi có thay đổi ở repo.
* AWS codepipeline đã tự cấu hình 1 endpoint URL cho webhooks của repo này.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.19.png) 
* Chọn nút **Next** sau khi xong để di chuyển qua stage Build.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.7.png) 

