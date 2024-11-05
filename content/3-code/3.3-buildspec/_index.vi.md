---
title : "Buildspec Codebuild"
date :  "`r Sys.Date()`" 
weight : 3 
chapter : false
pre : " <b> 3.3. </b> "
---

```yaml
version: 0.2

phases:
  install:
    runtime-versions:
      python: 3.12
    commands:
      - pip install --upgrade pip

  pre_build:
    commands:
      - echo "Preparing Lambda function files without zipping"
      - mkdir -p python
      - pip install -r lambda_functions/lambda_layer_requirements.txt -t python
      - echo "Contents of python directory:"
      - ls -R python  # List files in the python directory for verification

  post_build:
    commands:
      - echo "Uploading lambda_layer contents to S3 with KMS encryption"
      - aws s3 cp python s3://$S3_BUCKET_NAME/layers/python --recursive --sse aws:kms
      - echo "Upload complete"

artifacts:
  discard-paths: yes  # This will remove the path structure in the output artifacts
  files:
    - lambda_functions/addItem/lambda_function.py  # Include addItem function
    - lambda_functions/removeItem/lambda_function.py  # Include removeItem function
  secondary-artifacts:
    addItemArtifact:
      files:
        - lambda_functions/addItem/lambda_function.py  # Artifact for addItem
      discard-paths: yes  # Discard parent directory paths
    removeItemArtifact:
      files:
        - lambda_functions/removeItem/lambda_function.py  # Artifact for removeItem
      discard-paths: yes  # Discard parent directory paths
    layerArtifact:
      files:
        - 'python/**/*'  # Including all files in the python directory for layer
```
* Có 3 phases chính được định nghĩa trong quá trình build. Ở đây có ba phases: install, pre_build, và post_build.

### Install

* Thiết lập môi trường Python 3.12 bằng cách chỉ định trong runtime-versions.
* Cập nhật pip để đảm bảo sử dụng phiên bản mới nhất.
* Đây là bước cài đặt ban đầu và đảm bảo môi trường sẵn sàng trước khi chạy lệnh khác.

### Pre_build

* Mục đích chính: Chuẩn bị các file cần thiết cho Lambda layer mà không cần phải đóng gói thành file zip.
* Tạo thư mục python và cài đặt tất cả các thư viện cần thiết vào thư mục đó từ file lambda_layer_requirements.txt.
* In ra cấu trúc thư mục python để xác minh các thư viện đã được cài đặt đúng cách.
* Không có build: Vì không cần biên dịch hoặc xử lý bổ sung, nên không cần giai đoạn build.

### post_build
* Mục đích chính: Tải các file trong thư mục python lên S3 để dùng làm Lambda layer, với mã hóa bằng KMS để bảo mật.
* aws s3 cp với tùy chọn --recursive để tải lên toàn bộ thư mục python.
* Sau khi hoàn thành, in thông báo xác nhận để biết quá trình đã hoàn tất.

### Artifacts
* discard-paths: Loại bỏ cấu trúc đường dẫn trong các artifact, giúp các file chỉ bao gồm tên mà không có cấu trúc thư mục phụ.

* files: Bao gồm các file Python cho các Lambda functions addItem và removeItem. Đây là các hàm chính sẽ được triển khai.

* secondary-artifacts

* addItemArtifact và removeItemArtifact: Định nghĩa hai artifact riêng biệt cho mỗi hàm addItem và removeItem, giúp mỗi hàm có thể được triển khai độc lập hoặc sử dụng trong các Lambda khác nhau.
* layerArtifact: Bao gồm tất cả các file trong thư mục python để tạo ra Lambda layer, giúp các Lambda functions có thể sử dụng các thư viện mà không phải đóng gói lại.