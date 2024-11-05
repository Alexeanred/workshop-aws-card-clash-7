---
title : "Bonus: Build code with Jenkins"
date : "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 5. </b> "
---
* In the codepipeline at the build stage, there is an **Add jenkins** section so we will try to use Jenkins to build the code.
### Create Jenkins server and configure roles
#### Role configuration
* Go to IAM, select the **Create role** button.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.3_.png) 
* Select service: EC2
* Add these permissions:
    * ```AmazonS3FullAccess```
    * ```AWSCodePipelineCustomActionAccess```
* Name the role: ```JenkinsAccess```
* Choose **Create role**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.4_.png) 
#### Configure ec2 instances and install Jenkins
* We configure the ec2 instance as usual and need to pay attention to: 
    * Select AMI: ubuntu 22.04
    * In the inbound rule security group allow port 8080 to access jenkins because jenkins runs port 8080.
    * Add the above IAM role
    * You should choose the instance type as t3.medium (costly) so that jenkins runs smoothly.
    * Install jenkins into ec2 instance: https://www.jenkins.io/doc/book/installing/linux/
    * After installation is complete, we can log in to the dashboard and start building the code.

### Build code with Jenkins
#### Install plugins and libraries
* A tip to keep jenkins running without loading for too long after each instance start is after logging into Jenkins, go to **Manage jenkins**, select **System**, edit the Jenkins URL according to the instance's IP address, select **Save**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.5_.png)
* First, we need to install Codepipeline plugins in jenkins.
* In **Manage jenkins**, select **Plugins**, select **Available plugins**.
* Search ```AWS CodePipeline Plugin```.
* After installation is complete, you will see the plugin displayed in **Installed plugins**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.6_.png)
* To run python code and install libraries, we need to install python and pip on the ec2 instance.
* Go to ec2 ubuntu instance and run to install.
```
sudo apt install -y python3
sudo apt install -y python3-pip
```
#### Create build project
* In Dashboard, select **New Item**:
* Item name: ```BuildJenkinsCodepipeline```.
* Type: **Freestyle project**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.7_.png)
* Select **Excecute concurrent builds if necessary**
* In SCM, select **AWS CodePipeline**.
* AWS Region: ```us-east-1```.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.8_.png)
* In Category, select **Build**.
* Provider: ```JenkinsProvider```
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.9_.png)
* Build Triggers select **Poll SCM**.
* Schedule: ```* * * * *```.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.10_.png)
* In Execute Shell, paste the command:
```
# Set version
echo "version: 0.2"

# Install phase
echo "Starting install phase..."
/usr/bin/python3 --version  # Kiểm tra phiên bản Python
/usr/bin/python3 -m pip install --upgrade pip

# Pre-build phase
echo "Starting pre_build phase..."
echo "Preparing Lambda function files without zipping"
mkdir -p python/python
/usr/bin/python3 -m pip install -r lambda_functions/lambda_layer_requirements.txt -t python/python

echo "Contents of python directory:"
ls -R python  # List files in the python directory for verification

# Post-build phase
echo "Skipping S3 upload to avoid API usage."
```
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.11_.png)
* In Post-Build Actions, select **AWS CodePipeline Publisher**.
We configure 3 artifacts as follows:
* Artifact 1:
    * Location: ```lambda_functions/addItem/```
    * Artifact name: ```addItemArtifact```
* Artifact 2:
    * Location: ```lambda_functions/removeItem/```
    * Artifact name: ```removeItemArtifact```
* Artifact 3:
    * Location: ```/var/lib/jenkins/workspace/BuildJenkinsCodepipeline1/python/```
    * Artifact name: ```layerArtifact```
* Select **Save**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.12_.png)
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.13_.png)

### Create pipeline
* Go to codepipeline, select **Create pipeline**.
* Select **Build custom pipeline**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.14_.png)
* Pipeline name: ```test-jenkins```.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.15_.png)
* Stage source we do the same as before.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.16_.png)
* Stage build, we select **Other build providers**.
* Select **Add jenkins**.
* Provider name: ```JenkinsProvider```.
* Server URL: copy IPv4 of ec2 instance
* Project name: copy the build project name created in jenkins.
* Select **next**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.17_.png)
* Select **Skip deploy stage** and select **Create pipeline**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.18_.png)
* After creating the pipeline, we go back to the pipeline we just created.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.19_.png)
* Edit the build stage to add 3 output artifacts as shown: ```layerArtifact```, ```removeItemArtifact```, ```addItemArtifact```.
* Select **Done** and remember **Save** to save the configuration.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.20_.png)
