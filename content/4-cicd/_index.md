---
title : "CI/CD PipeLine"
date : "`r Sys.Date()`" 
weight : 4 
chapter : false
pre : " <b> 4. </b> "
---
* On AWS management console:
  * Find **CodePipeline**.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.1.png) 
* Select **Create pipeline**:
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.2.png) 
* Select **Build custom pipeline**.
* Select **Next**.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.3.png) 
* Fill in pipeline name: ```ci-cd-pipeline```
* Select **New service role**.
* Fill in role name: can fill in as shown.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.4.png) 
* Expand the Advanced settings section, you will see the Artifact store and Encryption key sections.
* In the architecture of AWS card clash, you will see an S3 bucket to store pipeline artifacts.
* Artifacts will be created as outputs of the stages of the pipeline.
* EncryptionKey is the encryption key used to encrypt the artifact, helping to secure data.
* CodePipeline only supports symmetric encryption. 
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.5.png)
