---
title : "Lambda functions"
date :  "`r Sys.Date()`" 
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
* Chúng ta sẽ viết những library ta cần cài đặt để chạy lambda functions. 
* Trong bài này mình sẽ minh họa việc cài đặt một lambda layer nên mình cài thử 1 thư viện không có sẵn như markdown.
* Nếu mình import markdown và chạy được lambda function thành công thì lambda layer đã được tạo thành công.
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
* Lambda function này có một hàm handler để nhận event từ API gateway để lấy ra được item ID và item Name sau đó viết item vào dynamodb table đã tạo.

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
* Lambda function này có một hàm handler để nhận event từ API gateway để lấy ra được item ID sau đó xóa item trên dynamodb table đã tạo.

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
* Ta sẽ kiểm tra function bằng thư viện moto, tạo 1 table, tạo 1 event với method là PUT và kiểm tra response trả về.

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

* Ở file này, ta sẽ tạo table, put vào 1 item trước và tạo event DELETE xóa item đó, trả về response.