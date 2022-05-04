---
title : "Tạo IAM Role"
date :  "`r Sys.Date()`" 
weight : 2 
chapter : false
pre : " <b> 2.2 </b> "
---
#### Tạo IAM Role
1. Truy cập giao diện [AWS Management Console](https://aws.amazon.com/console/)

![Create IAM Role](/images/2-Prerequiste/2.2-CreateIAMRole/0001-createiamrole.png)

2. Trong giao diện **IAM**

- Chọn **Roles**
- Chọn **Create role**

![Create IAM Role](/images/2-Prerequiste/2.2-CreateIAMRole/0002-createiamrole.png)

3. Trong giao diện **Select trusted entity**

- Chọn **AWS service**
- **Use case**, chọn **EC2**
- Chọn **Next**

![Create IAM Role](/images/2-Prerequiste/2.2-CreateIAMRole/0003-createiamrole.png)

4. Trong giao diện **Create role**

- Tìm policy **AdministratorAccess**
- Chọn policy **AdministratorAccess**
- Chọn **Next**

![Create IAM Role](/images/2-Prerequiste/2.2-CreateIAMRole/0004-createiamrole.png)

5. Trong giao diện **Role details**

- **Role name**, nhập ```CloudFormation-Role```

![Create IAM Role](/images/2-Prerequiste/2.2-CreateIAMRole/0005-createiamrole.png)

6. Chọn **Create role**

![Create IAM Role](/images/2-Prerequiste/2.2-CreateIAMRole/0006-createiamrole.png)

7. Hoàn thành tạo role

![Create IAM Role](/images/2-Prerequiste/2.2-CreateIAMRole/0007-createiamrole.png)

