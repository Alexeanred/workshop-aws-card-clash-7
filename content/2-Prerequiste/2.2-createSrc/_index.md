---
title : "Source Code on Github"
date : "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 2.2 </b> "
---

* You can go to the repo link I created to fork the repo to your github: https://github.com/Alexeanred/aws-card-clash-7-lambda-function.

* The file structure is 1 buildspec.yml file and 1 lambda_functions folder containing:
  * The 2 folders addItem and removeItem contain the code of 2 lambda functions and test files.
  * 1 requirement file to contain the libraries that need to be installed for the lambda layer. 
  * 1 yaml file to define cloudformation template.
