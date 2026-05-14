---
title : "Connecting EC2 Instance"
date: 2024-01-01
weight : 3
chapter : false
pre : " <b> 4.1.3 </b> "
---

#### Connecting EC2 Instance

1. In the [AWS Management Console](https://aws.amazon.com/console/)

- Find **Systems Manager**
- Select **Systems Manager**

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0001-Connectinstance.png)

2. In the **AWS Systems Manager** interface

- Select **Parameter Store**

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0002-Connectinstance.png)

3. In the **Parameters Store** interface

- Select **My parameters**
- Select **MyKey01**

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0003-Connectinstance.png)

4. In the **MyKey01** interface

- Select **Overview**
- See **Last modified user**
- Select **Show** Value

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0004-Connectinstance.png)

5. Copy the value and save **cloudformation.pem**

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0005-Connectinstance.png)

6. Use PuTTY Key Generator to load key and **Save private key**

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0006-Connectinstance.png)

7. Use **PuTTY** to connect **EC2 Instance**

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0007-Connectinstance.png)

8. When connecting enter user name:**ec2-user**

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0008-Connectinstance.png)

9. Use **ifconfig -a** command to display information of all network interfaces

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/0009-Connectinstance.png)

10. Do **ping amazon.com -c5** to test the connection

![Connect to EC2 instance](/images/4-CloudFormationadvance/4.1-Customresource/4.1.3-Connectinstance/00010-Connectinstance.png)