---
title : "Tạo Lambda Function"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 4.1.1 </b> "
---

#### Tạo Lambda Function

1. Truy cập vào giao diện [AWS Management Console](https://aws.amazon.com/console/)

- Tìm **IAM**
- Chọn **IAM**

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/0001-createlambdafunction.png)

2. Trong giao diện **IAM**

- Chọn **Roles**
- Chọn **Create role**

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/0002-createlambdafunction.png)

3. Trong bước **Select trusted entity**

- **Trusted entity type**, chọn **AWS service**
- **User case**, chọn **Lambda**
- Chọn **Next**

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/0003-createlambdafunction.png)


4. Trong bước **Add permissions**

- Chọn **Add permissions**
- Chọn **Create Policy**

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/0004-createlambdafunction.png)

5. Trong giao diện **Create policy**

- Chọn **JSON**
- Sao chép và dán vào đoạn mã sau:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
            ],
            "Resource": "arn:aws:logs:*:*:*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:CreateKeyPair",
                "ec2:DescribeKeyPairs",
                "ssm:PutParameter"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DeleteKeyPair",
                "ssm:DeleteParameter"
            ],
            "Resource": "*"
        }
    ]
}
```

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/0005-createlambdafunction.png)

6. Chọn **Next: Review**

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/0006-createlambdafunction.png)

7. Trong giao diện **Review policy**

- **Name**, nhập ```ssh-key-gen-policy```
- Xem các dịch vụ được **Allow**
- Chọn **Create policy**

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/0007-createlambdafunction.png)

8. Quay lại giao diện tạo role

- Chọn policy vừa tạo

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/0008-createlambdafunction.png)

9. Chọn **Next**

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/0009-createlambdafunction.png)

10. Trong giao diện **Role details**

- **Role name**, nhập ```ssh-key-gen-role```

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/00010-createlambdafunction.png)

11. Kiểm tra lại và chọn **Create role**

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/00011-createlambdafunction.png)

12. Trong giao diện **IAM**

- Tìm **ssh-key-gen-role**
- Xem role đã tạo

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/00012-createlambdafunction.png)

13. Trong giao diện [AWS Management Console](https://aws.amazon.com/console/)

- Tìm **Lambda**
- Chọn **Lambda**

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/00013-createlambdafunction.png)

14. Trong giao diện **AWS Lambda**

- Chọn **Functions**
- Chọn **Create function**

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/00014-createlambdafunction.png)


15. Trong giao diện **Create funtion**

- Chọn **Author from scratch**
- **Function name**, nhập ```ssh-key-gen```
- **Run time**, chọn **Python 3.9**
- Chọn **x86_64**

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/00015-createlambdafunction.png)

16. Trong giao diện **Permissions**

- Chọn **Use an existing role**
- Chọn **ssh-key-gen-role**

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/00016-createlambdafunction.png)

17.  Trong nội dung chỉnh sửa Function code, nhập vào nội dung mã lệnh như sau:

```
"""
This lambda implements the custom resource handler for creating an SSH key
and storing in in SSM parameter store.

e.g.

SSHKeyCR:
    Type: Custom::CreateSSHKey
    Version: "1.0"
    Properties:
      ServiceToken: !Ref FunctionArn
      KeyName: MyKey

An SSH key called MyKey will be created.

"""

from json import dumps
import sys
import traceback
import urllib.request

import boto3


def log_exception():
    "Log a stack trace"
    exc_type, exc_value, exc_traceback = sys.exc_info()
    print(repr(traceback.format_exception(
        exc_type,
        exc_value,
        exc_traceback)))


def send_response(event, context, response):
    "Send a response to CloudFormation to handle the custom resource lifecycle"

    responseBody = { 
        'Status': response,
        'Reason': 'See details in CloudWatch Log Stream: ' + \
            context.log_stream_name,
        'PhysicalResourceId': context.log_stream_name,
        'StackId': event['StackId'],
        'RequestId': event['RequestId'],
        'LogicalResourceId': event['LogicalResourceId'],
    }

    print('RESPONSE BODY: \n' + dumps(responseBody))

    data = dumps(responseBody).encode('utf-8')
    
    req = urllib.request.Request(
        event['ResponseURL'], 
        data,
        headers={'Content-Length': len(data), 'Content-Type': ''})
    req.get_method = lambda: 'PUT'

    try:
        with urllib.request.urlopen(req) as response:
            print(f'response.status: {response.status}, ' + 
                  f'response.reason: {response.reason}')
            print('response from cfn: ' + response.read().decode('utf-8'))
    except urllib.error.URLError:
        log_exception()
        raise Exception('Received non-200 response while sending ' +\
            'response to AWS CloudFormation')

    return True


def custom_resource_handler(event, context):
    '''
    This function creates a PEM key, commits it as a key pair in EC2, 
    and stores it, encrypted, in SSM.

    To retrieve the key with currect RSA format, you must use the command line: 

    aws ssm get-parameter \
        --name <KEYNAME> \
        --with-decryption \
        --region <REGION> \
        --output text

    Copy the values from (and including) -----BEGIN RSA PRIVATE KEY----- to 
    -----END RSA PRIVATE KEY----- into a file.

    To use it, change the permissions to 600
    Ensure to bundle the necessary packages into the zip stored in S3

    '''
    print("Event JSON: \n" + dumps(event))

    # session = boto3.session.Session()
    # region = session.region_name

    # Original
    # pem_key_name = os.environ['KEY_NAME']
    
    pem_key_name = event['ResourceProperties']['KeyName']

    response = 'FAILED'
    
    ec2 = boto3.client('ec2')

    if event['RequestType'] == 'Create':
        try:
            print("Creating key name %s" % str(pem_key_name))

            key = ec2.create_key_pair(KeyName=pem_key_name)
            key_material = key['KeyMaterial']
            ssm_client = boto3.client('ssm')
            param = ssm_client.put_parameter(
                Name=pem_key_name, 
                Value=key_material, 
                Type='SecureString')

            print(param)
            print(f'The parameter {pem_key_name} has been created.')

            response = 'SUCCESS'

        except Exception as e:
            print(f'There was an error {e} creating and committing ' +\
                f'key {pem_key_name} to the parameter store')
            log_exception()
            response = 'FAILED'

        send_response(event, context, response)

        return

    if event['RequestType'] == 'Update':
        # Do nothing and send a success immediately
        send_response(event, context, response)
        return

    if event['RequestType'] == 'Delete':
        #Delete the entry in SSM Parameter store and EC2
        try:
            print(f"Deleting key name {pem_key_name}")

            ssm_client = boto3.client('ssm')
            rm_param = ssm_client.delete_parameter(Name=pem_key_name)

            print(rm_param)

            _ = ec2.delete_key_pair(KeyName=pem_key_name)

            response = 'SUCCESS'
        except Exception as e:
            print(f"There was an error {e} deleting the key {pem_key_name} ' +\
            from SSM Parameter store or EC2")
            log_exception()
            response = 'FAILED'
         
        send_response(event, context, response)


def lambda_handler(event, context):
    "Lambda handler for the custom resource"

    try:
        return custom_resource_handler(event, context)
    except Exception:
        log_exception()
        raise
```


- Hãy cùng nhau phân tích đoạn mã lệnh này có những nội dung gì nhé

- **Thứ nhất: Hàm Handler** - mọi lambda function đều có một hàm handler và chúng được gọi khi có bất kì một sự kiện nào xảy ra. Nội dung của hàm Handler chỉ đơn giản gọi tới một hàm khác chứa nội dung xử lý cụ thể với sự kiện vừa xảy ra.

```
def lambda_handler(event, context):
    "Lambda handler for the custom resource"

    try:
        return custom_resource_handler(event, context)
    except Exception:
        log_exception()
        raise
```

- **Thứ hai: hàm custom_resource_handler** - là hàm chứa nội dung xử lý chi tiết khi có sự sự kiện xảy ra. Cụ thể, hàm sẽ thực hiện xác định loại yêu cầu và gửi trả phản hồi lại cho CloudFormation.

```
if event['RequestType'] == 'Create':
   try:
       print("Creating key name %s" % str(pem_key_name))

       key = ec2.create_key_pair(KeyName=pem_key_name)
       key_material = key['KeyMaterial']
       ssm_client = boto3.client('ssm')
       param = ssm_client.put_parameter(
           Name=pem_key_name, 
           Value=key_material, 
           Type='SecureString')

       print(param)
       print(f'The parameter {pem_key_name} has been created.')

       response = 'SUCCESS'

   except Exception as e:
       print(f'There was an error {e} creating and committing ' +\
           f'key {pem_key_name} to the parameter store')
       log_exception()
       response = 'FAILED'

   send_response(event, context, response)

   return
```

- **Thứ ba: hàm send_response** - là hàm gửi tra kết quả phản hồi cho CloudFormation endpoint dựa trên phương thức HTTP PUTS.

```
def send_response(event, context, response):
    "Send a response to CloudFormation to handle the custom resource lifecycle"

    responseBody = { 
        'Status': response,
        'Reason': 'See details in CloudWatch Log Stream: ' + \
            context.log_stream_name,
        'PhysicalResourceId': context.log_stream_name,
        'StackId': event['StackId'],
        'RequestId': event['RequestId'],
        'LogicalResourceId': event['LogicalResourceId'],
    }

    print('RESPONSE BODY: \n' + dumps(responseBody))

    data = dumps(responseBody).encode('utf-8')
    
    req = urllib.request.Request(
        event['ResponseURL'], 
        data,
        headers={'Content-Length': len(data), 'Content-Type': ''})
    req.get_method = lambda: 'PUT'

    try:
        with urllib.request.urlopen(req) as response:
            print(f'response.status: {response.status}, ' + 
                  f'response.reason: {response.reason}')
            print('response from cfn: ' + response.read().decode('utf-8'))
    except urllib.error.URLError:
        log_exception()
        raise Exception('Received non-200 response while sending ' +\
            'response to AWS CloudFormation')

    return True
```

- Chỉnh sửa code và chọn **Deploy**

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/00017-createlambdafunction.png)

18.  Sau khi lưu lại Function, thực hiện sao chép Function ARN ra mục ghi nhớ nào đó. Thông tin này sẽ được sử dung ở phần sau của bài hướng dẫn.

![Create Lambda Function](/images/4-CloudFormationadvance/4.1-Customresource/4.1.1-CreateLambdaFunction/00018-createlambdafunction.png)
