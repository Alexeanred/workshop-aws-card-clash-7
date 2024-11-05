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