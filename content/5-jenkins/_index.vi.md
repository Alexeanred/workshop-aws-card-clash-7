---
title : "Bonus: Build code với Jenkins"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 5. </b> "
---
* Trong codepipeline ở build stage, có phần **Add jenkins** vì thế ta sẽ thử sử dụng Jenkins để build code nha.
### Tạo Jenkins server và cấu hình role
#### Cấu hình role
* Vào IAM, chọn nút **Create role**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.3_.png) 
* Chọn service: EC2
* Thêm các permissions này vào:
    * ```AmazonS3FullAccess```
    * ```AWSCodePipelineCustomActionAccess```
* Đặt tên role: ```JenkinsAccess```
* Chọn **Create role**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.4_.png) 
#### Cấu hình ec2 instances và cài đặt Jenkins
* Ta cấu hình ec2 instance như thường lệ và cần chú ý: 
    * Chọn AMI: ubuntu 22.04
    * Ở inbound rule security group allow port 8080 để truy cập vào jenkins vì jenkins chạy port 8080.
    * Thêm IAM role trên vào
    * Nên chọn instance type là t3.medium (tốn phí) để jenkins chạy mượt.
    * Cài đặt jenkins vào ec2 instance: https://www.jenkins.io/doc/book/installing/linux/
    * Sau khi cài đặt xong, login vào được dashboard thì ta bắt đầu build code thôi.

### Build code với Jenkins
#### Install plugins và các thư viện
* Một tips để jenkins chạy không bị load lâu sau mỗi lần start instance là sau khi login vào Jenkins, ta vào **Manage jenkins**, chọn **System**, sửa lại Jenkins URL theo IP address của instance, chọn **Save**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.5_.png)
* Đầu tiên, ta cần cài đặt plugins Codepipeline ở jenkins.
* Ở **Manage jenkins**, chọn **Plugins**, chọn **Available plugins**.
* Tìm ```AWS CodePipeline Plugin```.
* Sau khi cài đặt xong ta thấy plugin được hiển thị ở **Installed plugins**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.6_.png)
* Để chạy được code python và install các library, ta cần cài đặt python và pip ở trên ec2 instance.
* Vào ec2 instance ubuntu và chạy để cài đặt.
```
sudo apt install -y python3
sudo apt install -y python3-pip
```
#### Tạo build project
* Ở Dashboard, chọn mục **New Item**:
* Item name: ```BuildJenkinsCodepipeline```.
* Type: **Freestyle project**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.7_.png)
* Chọn **Excecute concurrent builds if neccessary**
* Ở SCM, chọn **AWS CodePipeline**.
* AWS Region: ```us-east-1```.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.8_.png)
* Ở Category, chọn **Build**.
* Provider: ```JenkinsProvider```
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.9_.png)
* Build Triggers chọn **Poll SCM**.
* Schedule: ```* * * * *```.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.10_.png)
* Ở Execute Shell, dán command vào:
```
# Set version
echo "version: 0.2"

# Install phase
echo "Starting install phase..."
/usr/bin/python3 --version  # Kiểm tra phiên bản Python
/usr/bin/python3 -m pip install --upgrade pip
/usr/bin/python3 -m pip install pytest moto boto3 markdown pyopenssl
/usr/bin/python3 -m pip install --upgrade pyopenssl cryptography

# Update PATH to include ~/.local/bin
export PATH=$PATH:/var/lib/jenkins/.local/bin

# Test phase
echo "Starting test phase..."
echo "Running tests for addItem..."
pytest lambda_functions/addItem/test_addItem.py --junitxml=addItem_report.xml

echo "Running tests for removeItem..."
pytest lambda_functions/removeItem/test_removeItem.py --junitxml=removeItem_report.xml

# Build phase
echo "Starting pre_build phase..."
echo "Preparing Lambda function files without zipping"
mkdir -p python/python
/usr/bin/python3 -m pip install -r lambda_functions/lambda_layer_requirements.txt -t python/python

echo "Contents of python directory:"
ls -R python  # List files in the python directory for verification

```
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.22_.png)
* Ở Post-Build Actions, chọn **AWS CodePipeline Publisher**.
Ta cấu hình 3 artifacts như sau:
* Artifact 1:
    * Location: ```lambda_functions/addItem/```
    * Artifact name: ```addItemArtifact```
* Artifact 2:
    * Location: ```lambda_functions/removeItem/```
    * Artifact name: ```removeItemArtifact```
* Artifact 3:
    * Location: ```/var/lib/jenkins/workspace/BuildJenkinsCodepipeline1/python/```
    * Artifact name: ```layerArtifact```
* Chọn **Save**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.12_.png)
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.13_.png)

### Tạo pipeline
* Vào codepipeline, chọn **Create pipeline**.
* Chọn **Build custom pipeline**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.14_.png)
* Pipeline name: ```test-jenkins```.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.15_.png)
* Stage source ta làm như cũ.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.16_.png)
* Stage build, ta chọn **Other build providers**.
* Chọn **Add jenkins**.
* Provider name: ```JenkinsProvider```.
* Server URL: copy IPv4 của ec2 instance
* Project name: copy tên build project đã tạo ở jenkins.
* Chọn **next**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.17_.png)
* Chọn **Skip deploy stage** và chọn **Create pipeline**.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.18_.png)
* Sau khi tạo xong pipeline, ta vào lại pipeline vừa tạo.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.19_.png)
* Edit lại stage build thêm 3 output artifacts như hình: ```layerArtifact```, ```removeItemArtifact```, ```addItemArtifact```.
* Chọn **Done** và nhớ **Save** để lưu lại cấu hình.
![clean](/workshop-aws-card-clash-7/images/5.fwd/5.20_.png)
