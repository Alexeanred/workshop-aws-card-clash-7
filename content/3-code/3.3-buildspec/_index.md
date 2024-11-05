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
* There are 3 main identified stages in the construction process. Here there are three stages: installation, pre_build and post_build.

### Setting

* Set up Python 3.12 environment by specifying in runtime versions.
* Update pip to ensure the latest version is used.
* This is the initial installation step and ensures the environment is ready before running another command.

### Pre_build

* Main purpose: Prepare necessary files for Lambda layer without having to package them into zip files.
* Create python folder and install all necessary libraries into that folder from lambda_layer_requirements.txt file.
* Print out the python directory structure to verify the libraries have been installed properly.
* No build: Since no compilation or additional processing is required, there is no need for a build phase.

### post_build
* Main purpose: Upload files in the python folder to S3 to use as Lambda layer, with encryption using KMS for security.
* aws s3 cp with --recursive option to upload entire python directory.
* Once completed, print a confirmation message to know the process is complete.

### Artifacts
* discard-paths: Removes path structures in artifacts, helping files include only names without sub-folder structures.

* files: Includes Python files for the Lambda functions addItem and removeItem. These are the main functions that will be implemented.

* secondary-artifacts

* addItemArtifact and removeItemArtifact: Define two separate artifacts for each function addItem and removeItem, allowing each function to be deployed independently or used in different Lambdas.
* layerArtifact: Includes all files in the python directory to create the Lambda layer, helping Lambda functions to use libraries without having to repackage.