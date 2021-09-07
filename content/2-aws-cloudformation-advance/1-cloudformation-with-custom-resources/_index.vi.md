+++
title = "Các Tài nguyên Tùy chỉnh"
date = 2020-04-18T00:38:32+07:00
weight = 1
chapter = false
pre = "<b>2.1. </b>"
+++

---
Bạn có thể mở rộng khả năng sử dụng của CloudFormation với các tài nguyên tùy chỉnh bằng cách ủy quyền công việc cần thực hiện cho một hàm Lambda đã được thiết kế đặc biệt, để tương tác với dịch vụ CloudFormation.  
Trong nội dung của mã code, bạn sẽ cài đặt các hàm tạo, cập nhật và xóa, và hàm gửi phản hồi về trạng thái của hoạt động đó.

Trong bài lab đầu tiên này, bạn sẽ tạo một tài nguyên tùy chỉnh mà có thể sinh khóa SSH và lưu trữ nó trong kho tham số SSM.
{{% notice tip %}}
Lưu ý: như đã đề cập ở phần đầu workshop, số lượng dịch vụ trên các region là không giống nhau, do vậy cần đảm bảo region các bạn thực hiện bài lab phải có đủ dịch vụ mà workshop yêu câu. Đối với bài lab này, chúng ta sẽ làm việc trên region **N.Virginia** với ID **us-east-1**
{{% /notice %}}

Chi tiết các bước thực hiện như sau: 

1 - Log in vào AWS account, truy cập [IAM Console](https://console.aws.amazon.com/iam/home#/home) rồi chọn **Role**. Bấm **Create Role** 
2 - Tại trang **Select type of trusted entity**, chọn Lambda, rồi bấm **Next: Permission**
![Security Hub](../../../images/15.png?width=90pc)
3 - Tại trang **Attach permissions policies**, bấm **Create Policy**. Một cửa sổ mới hiện tr, nhập vào nội dung bên dưới rồi bấm **Review Policy**
![Security Hub](../../../images/16.png?width=90pc)
```python
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
4 - Nhập tên cho Policy mới ***ssh-key-gen-policy***, rồi bấm **Create Policy**
![Security Hub](../../../images/17.png?width=90pc)
5 - Trở lại trang **Attach permissions policies**, thực hiện filter để tìm Policy mới tạo có tên ***ssh-key-gen-policy***. Chọn rồi bấm **Next:Tags** >> **Next:Review**
![Security Hub](../../../images/15.png?width=90pc)
6 - Nhập tên cho Role mới ***ssh-key-gen-role***, rồi bấm **Create Role**
![Security Hub](../../../images/19.png?width=90pc)
7 - Truy cập [Lambda console](https://console.aws.amazon.com/lambda/home?region=us-east-1#), chọn lại region thành **N.Virginia**, rồi bấm **Create Functio**n
![Security Hub](../../../images/20.png?width=90pc)
8 - Tại trang **Create Function**, chọn **Author From Scratch**.  
9 - Nhập tên cho **Function Name**: ***ssh-key-gen***  
10 - Với **Runtime** chọn **Python 3.6**   
11. Với **Execution role**, chọn **Use an existing role**, trỏ tới Role mới tạo ***ssh-key-gen-role*** rồi bấm **Create Function**
![Security Hub](../../../images/21.png?width=90pc)
12. Trong nội dung chỉnh sửa Function code, nhập vào nội dung mã lệnh như file **custom_resource_lambda.py** bên dưới {{% attachments pattern="custom_resource_lambda.py" /%}} 

Hãy cùng nhau phân tích đoạn mã lệnh này có những nội dung gì nhé
- Thứ nhất: Hàm ***Handler*** - mọi lambda function đều có một hàm handler và chúng được gọi khi có bất kì một sự kiện nào xảy ra. Nội dung của hàm Handler chỉ đơn giản gọi tới một hàm khác chứa nội dung xử lý cụ thể với sự kiện vừa xảy ra.
```python
def lambda_handler(event, context):
    "Lambda handler for the custom resource"

    try:
        return custom_resource_handler(event, context)
    except Exception:
        log_exception()
        raise
```
- Thứ hai: hàm ***custom_resource_handler*** - là hàm chứa nội dung xử lý chi tiết khi có sự sự kiện xảy ra. Cụ thể, hàm sẽ thực hiện xác định loại yêu cầu và gửi trả phản hồi lại cho CloudFormation. 
```python
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
- Thứ ba: hàm ***send_response*** - là hàm gửi tra kết quả phản hồi cho CloudFormation endpoint dựa trên phương thức HTTP PUTS. 
```python
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
Sau khi lưu lại Function, thực hiện sao chép **Function ARN** ra mục ghi nhớ nào đó. Thông tin này sẽ được sử dung ở phần sau của bài hướng dẫn. 
![Security Hub](../../../images/22.png?width=90pc) 
13 - Truy cập CloudFormation console, bấm **Create Stack** rồi bấm chọn **Upload a template file** rồi upload file **custom_resource_cfn.yml** được attach bên dưới. 
{{% attachments pattern="custom_resource_cfn.yml" /%}}  

14 - Nhập tên Stack ***ssh-key-gen-cr***, và dán **Function ARN** ta mới sao chép ở trên vào khung giá trị **FunctionArn**, đồng thời hoàn thiện nốt các thông tin còn lại rồi bấm **Next** cho tới bước cuối cùng bấm **Create Stack**
![Security Hub](../../../images/23.png?width=90pc)  

15 - Mất khoảng vài phút để Stack có thể hoàn thành tiến trình chạy và cho ra kết quả gồm 1 EC2 Instance với DNS name và địa chỉ Public IP của nó 
![Security Hub](../../../images/24.png?width=90pc)   

16 - Truy cập **EC2 Console** để thấy một EC2 Instance cũng đã được khởi tạo thành công. 
![Security Hub](../../../images/25.png?width=90pc)  

17 - Vào trang **Systems Manager Console**, mở tab **Parameter Store** bạn sẽ thấy có một Key mới được tạo ra thời điểm gần đây, nó chính là khóa chứa thông tin cấu hình Secure String (SSH key) mà sử dụng KMS để mã hóa các giá trị tham số. 
![Security Hub](../../../images/26.png?width=90pc)  

18 - Download SSH key từ Parameter Store rồi lưu thành file **.pem** với *permissions* được set thành *600* nếu laptop chạy Linux hoặc Mac.  

19 - Thực hiện log in vào EC2 instance mới được tạo với SSH key vừa được download để kiểm tra Stack đã khởi tạo SSH Key cho EC2 Instance một cách tự động thành công.
![Security Hub](../../../images/27.png?width=90pc)  

20 - Xóa bỏ stack và lambda function.