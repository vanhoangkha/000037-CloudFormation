---
title : "Tạo IAM User"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 2.1 </b> "
---
#### Tạo IAM User

1. Mở giao diện **[AWS Management Console](https://aws.amazon.com/console/)**

- Tìm **IAM**
- Chọn **IAM**

![Create IAM User](/images/2-Prerequiste/2.1-CreateIAMUser/0001-createiamuser.png)

2. Trong giao diện **IAM**

- Chọn **Users**
- Chọn **Add users**

![Create IAM User](/images/2-Prerequiste/2.1-CreateIAMUser/0002-createiamuser.png)'

3. Trong phần **Add user**

- **User name**, nhập ```CloudFormation-user```
- Chọn **Access key - Programmatic access**
- Chọn **Password - AWS Management Console access**
- Chọn **Custom password**
- Nhập password
- Chọn **Show password**
- Bỏ chọn **Require password reset**
- Chọn **Next:Permissions**

![Create IAM User](/images/2-Prerequiste/2.1-CreateIAMUser/0003-createiamuser.png)

4. Trong giao diện **Set permissions**

- Chọn **Attach existing policies directly**
- Tìm và chọn **AdministratorAccess**
- Chọn **Next:Tags**

![Create IAM User](/images/2-Prerequiste/2.1-CreateIAMUser/0004-createiamuser.png)

5. Trong giao diện **Add tags**

- Nhập **Key**
- Nhập **Value**
- Chọn **Next:Review**

![Create IAM User](/images/2-Prerequiste/2.1-CreateIAMUser/0005-createiamuser.png)

6. Kiểm tra lại thông tin user và chọn **Create user**

![Create IAM User](/images/2-Prerequiste/2.1-CreateIAMUser/0006-createiamuser.png)

7. Tạo user thành công

- Xem thông tin về **Access key ID** và **Secret access key**
- Chọn **Download.csv**
- Chọn **Close**

![Create IAM User](/images/2-Prerequiste/2.1-CreateIAMUser/0007-createiamuser.png)

8. Vậy đã tạo user thành công

![Create IAM User](/images/2-Prerequiste/2.1-CreateIAMUser/0008-createiamuser.png)