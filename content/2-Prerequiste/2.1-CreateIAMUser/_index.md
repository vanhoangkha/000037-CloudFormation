---
title : "Create IAM User"
date: 2024-01-01
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---
#### Create IAM User

1. Open interface **[AWS Management Console](https://aws.amazon.com/console/)**

- Find **IAM**
- Select **IAM**

![Create IAM User](/images/2-Prerequiste/2.1-CreateIAMUser/0001-createiamuser.png)

2. In the **IAM** interface

- Select **Users**
- Select **Add users**

![Create IAM User](/images/2-Prerequiste/2.1-CreateIAMUser/0002-createiamuser.png)'

3. In the **Add user** section

- **User name**, enter ```CloudFormation-user```
- Select **Access key - Programmatic access**
- Select **Password - AWS Management Console access**
- Select **Custom password**
- Enter password
- Select **Show password**
- Uncheck **Require password reset**
- Select **Next:Permissions**

![Create IAM User](/images/2-Prerequiste/2.1-CreateIAMUser/0003-createiamuser.png)

4. In the **Set permissions** interface

- Select **Attach existing policies directly**
- Find and select **AdministratorAccess**
- Select **Next:Tags**

![Create IAM User](/images/2-Prerequiste/2.1-CreateIAMUser/0004-createiamuser.png)

5. In the **Add tags** interface

- Enter **Key**
- Enter **Value**
- Select **Next:Review**

![Create IAM User](/images/2-Prerequiste/2.1-CreateIAMUser/0005-createiamuser.png)

6. Check the user information and select **Create user**

![Create IAM User](/images/2-Prerequiste/2.1-CreateIAMUser/0006-createiamuser.png)

7. Create user successfully

- View information about **Access key ID** and **Secret access key**
- Select **Download.csv**
- Select **Close**

![Create IAM User](/images/2-Prerequiste/2.1-CreateIAMUser/0007-createiamuser.png)

8. So the user has been created successfully

![Create IAM User](/images/2-Prerequiste/2.1-CreateIAMUser/0008-createiamuser.png)