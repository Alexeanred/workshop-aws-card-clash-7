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
