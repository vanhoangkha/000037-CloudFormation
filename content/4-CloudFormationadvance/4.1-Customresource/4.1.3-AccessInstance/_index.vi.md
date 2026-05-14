---
title : "Kết nối EC2 Instance"
date: 2024-01-01
weight : 3
chapter : false
pre : " <b> 4.1.3 </b> "
---

#### Kết nối EC2 Instance

1. Trong giao diện [AWS Management Console](https://aws.amazon.com/console/)

- Tìm **Systems Manager**
- Chọn **Systems Manager**

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0001-Connectinstance.png)

2. Trong giao diện **AWS Systems Manager**

- Chọn **Parameter Store**

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0002-Connectinstance.png)

3. Trong giao diện **Parameters Store**

- Chọn **My parameters**
- Chọn **MyKey01**

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0003-Connectinstance.png)

4. Trong giao diện **MyKey01**

- Chọn **Overview**
- Xem **Last modified user**
- Chọn **Show** Value

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0004-Connectinstance.png)

5. Sao chép value và lưu **cloudformation.pem**

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0005-Connectinstance.png)

6. Sử dụng PuTTY Key Generator load key và **Save private key**

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0006-Connectinstance.png)

7. Sử dụng **PuTTY** để kết nối **EC2 Instance**

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0007-Connectinstance.png)

8. Khi kết nối nhập user name:**ec2-user**

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0008-Connectinstance.png)

9. Sử dụng lệnh **ifconfig -a** để hiển thị thông tin của tất cả giao diện mạng

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0009-Connectinstance.png)

10. Thực hiện **ping amazon.com -c5** để kiểm tra kết nối

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/00010-Connectinstance.png)