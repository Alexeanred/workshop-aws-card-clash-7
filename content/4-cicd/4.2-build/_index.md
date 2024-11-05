---
title : "Build Stage"
date : "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 4.2 </b> "
---
* At the build stage, we select **Other build providers**.
* Select **AWS CodeBuild**.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.8.png) 
* Select **Creating project**.
* Project name is named ```build_lambda_code```.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.9.png) 
* The configurations of the codebuild virtual machine used to build code are set to default.\
* Select **New service role**.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.10.png)
* In buildspec, there are 2 options:
    * Initially, I chose **insert build commands** to write directly to the editor.
    * You can choose **Use a buildspec file** and leave buildspec.yml in the github repository for codebuild to retrieve and build the code.

* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.11.png) 
* Select **Continue to CodePipeline**.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.20.png) 
* After creating the build project, we can pre-record the Environemnt variables of the codebuild as ```S3_BUCKET_NAME``` or after creating it, we can add it later.
* Codebuild will receive input artifacts from SourceArtifact.
* Select the **Next** button to go to the deploy stage.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.12.png)