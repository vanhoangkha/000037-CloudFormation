---
title : "Create IAM Role"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---
#### Create IAM Role
1. Access the [AWS Management Console](https://aws.amazon.com/console/)

![Create IAM Role](/images/2-Prerequiste/2.2-CreateIAMRole/0001-createiamrole.png)

2. In the **IAM** interface

- Select **Roles**
- Select **Create role**

![Create IAM Role](/images/2-Prerequiste/2.2-CreateIAMRole/0002-createiamrole.png)

3. In the **Select trusted entity** interface

- Select **AWS service**
- **Use case**, select **EC2**
- Select **Next**

![Create IAM Role](/images/2-Prerequiste/2.2-CreateIAMRole/0003-createiamrole.png)

4. In the **Create role** interface

- Find the policy **AdministratorAccess**
- Select the policy **AdministratorAccess**
- Select **Next**

![Create IAM Role](/images/2-Prerequiste/2.2-CreateIAMRole/0004-createiamrole.png)

5. In the **Role details** interface

- **Role name**, enter ```CloudFormation-Role```

![Create IAM Role](/images/2-Prerequiste/2.2-CreateIAMRole/0005-createiamrole.png)

6. Select **Create role**

![Create IAM Role](/images/2-Prerequiste/2.2-CreateIAMRole/0006-createiamrole.png)

7. Complete role creation

![Create IAM Role](/images/2-Prerequiste/2.2-CreateIAMRole/0007-createiamrole.png)