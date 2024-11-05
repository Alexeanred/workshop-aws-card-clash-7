---
title : "Cloudformation template"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 3.2. </b> "
---

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Description: CloudFormation template for creating DynamoDB, API Gateway, Lambda functions with a shared layer for item management.

Parameters:
  DynamoDBTableName:
    Type: String
    Description: Name of the DynamoDB table to store items.
    Default: ShoppingItem

  ArtifactS3Bucket:
    Type: String
    Default: codepipeline-us-east-1-534781109788
    
  BucketName:
    Type: String
    Description: Name of the S3 bucket where the Lambda artifacts are stored.

  AddItemObjectKey:
    Type: String
    Description: S3 Key of the AddItem Lambda artifact.

  RemoveItemObjectKey:
    Type: String
    Description: S3 Key of the RemoveItem Lambda artifact.

  LayerObjectKey:
    Type: String
    Description: S3 Key of the Lambda layer artifact.



Resources:
  # DynamoDB Table
  MyDynamoDBTable:
    Type: AWS::DynamoDB::Table
    Properties:
      TableName: !Ref DynamoDBTableName
      AttributeDefinitions:
        - AttributeName: itemId
          AttributeType: S
      KeySchema:
        - AttributeName: itemId
          KeyType: HASH
      ProvisionedThroughput:
        ReadCapacityUnits: 5
        WriteCapacityUnits: 5

  # Lambda Layer
  LambdaLayer:
    Type: AWS::Lambda::LayerVersion
    Properties:
      LayerName: CommonLambdaLayer
      Description: Layer for shared dependencies
      CompatibleRuntimes:
        - python3.12
      Content:
        S3Bucket: !Ref BucketName
        S3Key: !Ref LayerObjectKey

  # AddItem Lambda Function
  AddItemLambdaFunction:
    Type: AWS::Lambda::Function
    Properties:
      FunctionName: AddItemFunction
      Handler: lambda_function.add_item_handler
      Runtime: python3.12
      Role: !GetAtt LambdaExecutionRole.Arn
      Code:
        S3Bucket: !Ref BucketName
        S3Key: !Ref AddItemObjectKey
      Layers:
        - !Ref LambdaLayer
      Environment:
        Variables:
          DYNAMODB_TABLE: !Ref DynamoDBTableName

  # RemoveItem Lambda Function
  RemoveItemLambdaFunction:
    Type: AWS::Lambda::Function
    Properties:
      FunctionName: RemoveItemFunction
      Handler: lambda_function.remove_item_handler
      Runtime: python3.12
      Role: !GetAtt LambdaExecutionRole.Arn
      Code:
        S3Bucket: !Ref BucketName  
        S3Key: !Ref RemoveItemObjectKey
      Layers:
        - !Ref LambdaLayer
      Environment:
        Variables:
          DYNAMODB_TABLE: !Ref DynamoDBTableName

  # Execution Role for Lambda Functions
  LambdaExecutionRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: LambdaExecutionRole
      AssumeRolePolicyDocument:
        Version: '2012-10-17'
        Statement:
          - Effect: Allow
            Principal:
              Service: lambda.amazonaws.com
            Action: sts:AssumeRole
      Policies:
        - PolicyName: DynamoDBAccessPolicy
          PolicyDocument:
            Version: '2012-10-17'
            Statement:
              - Effect: Allow
                Action:
                  - dynamodb:PutItem
                  - dynamodb:DeleteItem
                Resource: !Sub "arn:aws:dynamodb:${AWS::Region}:${AWS::AccountId}:table/${DynamoDBTableName}"
              - Effect: Allow
                Action:
                  - logs:CreateLogGroup
                  - logs:CreateLogStream
                  - logs:PutLogEvents
                Resource: "arn:aws:logs:*:*:*"


  # API Gateway for AddItem
  AddItemApi:
    Type: AWS::ApiGateway::RestApi
    Properties:
      Name: AddItemApiGateway

  AddItemResource:
    Type: AWS::ApiGateway::Resource
    Properties:
      ParentId: !GetAtt AddItemApi.RootResourceId
      PathPart: additem
      RestApiId: !Ref AddItemApi

  AddItemMethod:
    Type: AWS::ApiGateway::Method
    Properties:
      AuthorizationType: NONE
      HttpMethod: POST
      ResourceId: !Ref AddItemResource
      RestApiId: !Ref AddItemApi
      Integration:
        Type: AWS_PROXY
        IntegrationHttpMethod: POST
        Uri: !Sub
          - arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${AddItemLambdaFunctionArn}/invocations
          - { AddItemLambdaFunctionArn: !GetAtt AddItemLambdaFunction.Arn }

  AddItemLambdaPermission:
    Type: AWS::Lambda::Permission
    Properties:
      FunctionName: !Ref AddItemLambdaFunction
      Action: lambda:InvokeFunction
      Principal: apigateway.amazonaws.com
      SourceArn: !Sub "arn:aws:execute-api:${AWS::Region}:${AWS::AccountId}:${AddItemApi}/*"

  # API Gateway for RemoveItem
  RemoveItemApi:
    Type: AWS::ApiGateway::RestApi
    Properties:
      Name: RemoveItemApiGateway

  RemoveItemResource:
    Type: AWS::ApiGateway::Resource
    Properties:
      ParentId: !GetAtt RemoveItemApi.RootResourceId
      PathPart: removeitem
      RestApiId: !Ref RemoveItemApi

  RemoveItemMethod:
    Type: AWS::ApiGateway::Method
    Properties:
      AuthorizationType: NONE
      HttpMethod: DELETE
      ResourceId: !Ref RemoveItemResource
      RestApiId: !Ref RemoveItemApi
      Integration:
        Type: AWS_PROXY
        IntegrationHttpMethod: DELETE
        Uri: !Sub
          - arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${RemoveItemLambdaFunctionArn}/invocations
          - { RemoveItemLambdaFunctionArn: !GetAtt RemoveItemLambdaFunction.Arn }

  RemoveItemLambdaPermission:
    Type: AWS::Lambda::Permission
    Properties:
      FunctionName: !Ref RemoveItemLambdaFunction
      Action: lambda:InvokeFunction
      Principal: apigateway.amazonaws.com
      SourceArn: !Sub "arn:aws:execute-api:${AWS::Region}:${AWS::AccountId}:${RemoveItemApi}/*"

Outputs:
  AddItemLambdaFunctionArn:
    Description: ARN of the Add Item Lambda function
    Value: !GetAtt AddItemLambdaFunction.Arn

  RemoveItemLambdaFunctionArn:
    Description: ARN of the Remove Item Lambda function
    Value: !GetAtt RemoveItemLambdaFunction.Arn

  LambdaLayerArn:
    Description: ARN of the shared Lambda Layer
    Value: !Ref LambdaLayer

  DynamoDBTableNameOutput:
    Description: Name of the DynamoDB table
    Value: !Ref MyDynamoDBTable

  AddItemApiId:
    Description: ID of the AddItem API Gateway
    Value: !Ref AddItemApi

  RemoveItemApiId:
    Description: ID of the RemoveItem API Gateway
    Value: !Ref RemoveItemApi
```

* In the above template, there are components Parameters, Resources, Outputs.
* The Parameters section has components we get when configuring Parameter Overrides at Cloudformation deploy stage such as BucketName, 3 ObjectKeys of 2 lambda functions, 1 lambda layer.
* Resources, we create dynamodb table, lambda layer, 2 lambda functions, lambda role has rights to dynamodb and write logs to cloudwatch, 2 API gateways.
* Outputs take the resource names of 2 lambda functions, layer, dynamodb, 2 api gateways.