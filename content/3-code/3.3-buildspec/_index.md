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
      - pip install pytest moto boto3 markdown

  pre_build:
    commands:
      - echo "Running tests for addItem"
      - pytest lambda_functions/addItem/test_addItem.py --junitxml=addItem_report.xml  # Chạy test cho addItem
      - echo "Running tests for removeItem"
      - pytest lambda_functions/removeItem/test_removeItem.py --junitxml=removeItem_report.xml  # Chạy test cho removeItem
  build:
    commands:
      - echo "Preparing Lambda function files without zipping"
      - mkdir -p python
      - pip install -r lambda_functions/lambda_layer_requirements.txt -t python
      - echo "Contents of python directory:"
      - ls -R python  # List files in the python directory for verification

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
* There are 3 main phases defined in the build process. There are three phases: install, pre_build, and build.

### Install

* Set up the Python 3.12 environment by specifying in runtime-versions.

* Update pip to ensure using the latest version and necessary libraries.

* This is the initial installation step and ensures the environment is ready before running other commands.

### Pre_build

* We use the Pytest library to run the 2 test files we wrote to test lambda functions, if the assertions are successful, we will go to the next step.

### build
* Main purpose: Prepare the necessary files for Lambda layer without having to package them into a zip file.

* Create a python directory and install all the necessary libraries into that directory from the lambda_layer_requirements.txt file.

* Print out the python directory structure to verify that the libraries are installed correctly.

### Artifacts
* discard-paths: Removes the path structure in the artifacts, making the files just the name without the sub-directory structure.

* files: Include the Python files for the Lambda functions addItem and removeItem. These are the main functions that will be deployed.

* secondary-artifacts

* addItemArtifact and removeItemArtifact: Define two separate artifacts for each addItem and removeItem function, allowing each function to be deployed independently or used in different Lambdas.

* layerArtifact: Include all the files in the python directory to create the Lambda layer, allowing Lambda functions to use libraries without having to repackage them.