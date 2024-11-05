---
title : "Source stage"
date : "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 4.1 </b> "
---
* In step 3, we configure the first stage as the source stage.
* Scroll down and select **Source provider** which is GitHub (via OAuth app).
* Verify the necessary information to authorize AWS to access Github.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.6.png) 
* After verification is complete, select the Repository created in the preparation step.
* Select the Branch we used to push the code to github.
* Select Github webhooks to trigger the pipeline when there are changes in the repo.
* AWS codepipeline has automatically configured a URL endpoint for this repo's webhooks.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.19.png) 
* Select the **Next** button after completion to move to the Build stage.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.7.png)