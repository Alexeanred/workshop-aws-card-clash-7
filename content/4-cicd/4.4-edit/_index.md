---
title : "Edit pipeline"
date : "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 4.4 </b> "
---
* After creating the pipeline, go to edit build stage:
    * Added Output artifacts and revised S3_BUCKET_NAME.
    * Added 3 output artifacts: addItemArtifact, removeItemArtifact, layerArtifact.
    * S3_BUCKET_NAME fixes bucket name for example: ```codepipeline-us-east-1-534781109788```.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.16.png) 
* Edit the deploy stage again:
    * Delete the old Input Artifact BuildArtifact, add 3 newly created aritfacts in the output of the build stage.
* ![cicd](/workshop-aws-card-clash-7/images/4.s3/4.17.png)