---
title : "Lambda functions"
date : "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.1. </b> "
---

## Requirements
```
markdown
boto3
botocore
```
* We will write the libraries we need to install to run lambda functions. 
* In this article, I will illustrate the installation of a lambda layer, so I tried to install an unavailable library like markdown.
* If I import markdown and run the lambda function successfully, the lambda layer has been created successfully.
## AddItem function

```python
import json
import boto3
from botocore.exceptions import ClientError
import markdown
# Khởi tạo DynamoDB client
dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('ShoppingItem')

def add_item_handler(event, context):
    print("Envent: ",event)
    # Nhận dữ liệu từ sự kiện (event)
    body = json.loads(event['body'])
    item_id = body['itemId']
    item_name = body['itemName']
    print(body)
    print(item_id)
    
    if not item_id or not item_name:
        return {
            'statusCode': 400,
            'body': json.dumps('itemId và itemName are compulsory.')
        }
    
    try:
        # Thêm item vào bảng DynamoDB
        table.put_item(
            Item={
                'itemId': item_id,
                'itemName': item_name
            }
        )
        return {
            'statusCode': 200,
            'body': json.dumps('Item is added successfully!')
        }
    except ClientError as e:
        return {
            'statusCode': 500,
            'body': json.dumps(f"Error when add item: {e.response['Error']['Message']}")
        }
```
* This Lambda function has a handler function to receive events from the API gateway to get the item ID and item Name then write the item to the created dynamodb table.

## RemoveItem function
```python
import json
import boto3
from botocore.exceptions import ClientError
import markdown
# Khởi tạo DynamoDB client
dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('ShoppingItem')

def remove_item_handler(event, context):
    # Nhận dữ liệu từ sự kiện (event)
    body = json.loads(event['body'])
    item_id = body.get('itemId')
    
    if not item_id:
        return {
            'statusCode': 400,
            'body': json.dumps('itemId is compulsory')
        }

    
    try:
        # Xóa item từ bảng DynamoDB
        table.delete_item(
            Key={
                'itemId': item_id
            }
        )
        return {
            'statusCode': 200,
            'body': json.dumps('Item is successfully deleted!')
        }
    except ClientError as e:
        return {
            'statusCode': 500,
            'body': json.dumps(f"Error when deleting item: {e.response['Error']['Message']}")
        }
```
* This Lambda function has a handler function to receive events from the API gateway to get the item ID and then delete the item on the created dynamodb table.

## AddItem test function

```python
import json
import boto3
from moto import mock_aws 
from lambda_function import add_item_handler

@mock_aws 
def test_handler_success():
    # Mock DynamoDB table
    dynamodb = boto3.resource('dynamodb')
    table_name = "ShoppingItem"
    dynamodb.create_table(
        TableName=table_name,
        KeySchema=[
            {"AttributeName": "itemId", "KeyType": "HASH"}
        ],
        AttributeDefinitions=[
            {"AttributeName": "itemId", "AttributeType": "S"}
        ],
        ProvisionedThroughput={"ReadCapacityUnits": 5, "WriteCapacityUnits": 5}
    )

    # Tạo sự kiện giả
    event = {
        "httpMethod": "PUT",
        "body": json.dumps({
            "itemId": "345",
            "itemName": "dog"
        })
    }

    # Gọi handler và kiểm tra kết quả
    response = add_item_handler(event, None)
    assert response["statusCode"] == 200
    assert json.loads(response["body"]) == "Item is added successfully!"
```
* We will test the function using the moto library, create a table, create an event with the PUT method and check the returned response.

## RemoveItem test function
```python
import json
import boto3
from moto import mock_aws  # Thay thế mock_dynamodb2 bằng mock_aws
from lambda_function import remove_item_handler

@mock_aws  # Sử dụng mock_aws thay vì mock_dynamodb2
def test_handler_success():
    # Mock DynamoDB table
    dynamodb = boto3.resource('dynamodb')
    table_name = "ShoppingItem"
    table = dynamodb.create_table(
        TableName=table_name,
        KeySchema=[
            {"AttributeName": "itemId", "KeyType": "HASH"}
        ],
        AttributeDefinitions=[
            {"AttributeName": "itemId", "AttributeType": "S"}
        ],
        ProvisionedThroughput={"ReadCapacityUnits": 5, "WriteCapacityUnits": 5}
    )
    table.put_item(
        Item={
            'itemId': '345',
            'itemName': 'dog'
        }
    )
    # Tạo sự kiện giả
    event = {
        "httpMethod": "DELETE",
        "body": json.dumps({
            "itemId": "345"
        })
    }

    # Gọi handler và kiểm tra kết quả
    response = remove_item_handler(event, None)
    assert response["statusCode"] == 200
    assert json.loads(response["body"]) == 'Item is successfully deleted!'
```

* In this file, we will create a table, put in an item first and create a DELETE event to delete that item, returning the response.