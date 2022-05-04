---
title : "Create Workspace"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---
#### Create Workspace

1. Access the interface [AWS Management Console](https://aws.amazon.com/console/)

- Find **Cloud9**
- Select **Cloud9**

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0001-createawscloud9.png)

2. In **AWS Cloud9** interface

- Select **Create environment**

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0002-createawscloud9.png)

3. In the **Create environment** interface

- **Name**, enter ```ASG-Cloud9-Workshop```
- Select **Next step**

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0003-createawscloud9.png)

5. In **Network settings**

- Select **Network (VPC)**
- Select **Public subnet**
- Select **Next step**


![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0005-createawscloud9.png)

6. Select **Create environment**


![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0006-createawscloud9.png)

7. Environment interface just initialized


![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0007-createawscloud9.png)

8. In the environment interface just initialized

- Select the **R** icon
- Select **Manage EC2 Instance**


![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0008-createawscloud9.png)

9. In the **EC2** interface

- Select **Action**
- Select **Security**
- Select **Modify IAM role**

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0009-createawscloud9.png)

10. In the **Modify IAM role** interface

- Select the created role, for this lab choose **CloudFormation-Role**
- Select **Save**

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00010-createawscloud9.png)

11. Completed role assignment successfully

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00011-createawscloud9.png)

12. In the view of the **AWS Cloud9** environment

- Select **AWS Cloud9**
- Select **Preferences**

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00012-createawscloud9.png)

13. Cloud9 will manage IAM credentials automatically. We will need to disable this feature and use the IAM Role.

- Select **AWS SETTINGS**
- Select **Credentials**
- Uncheck **AWS managed temporary credentials**

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00013-createawscloud9.png)

14. Copy and Paste the command below into the Terminal of Cloud9 Workspace to install tools to support text processing on the command line.

```
sudo yum -y install jq gettext bash-completion moreutils
```

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00014-createawscloud9.png)

15. Install tool cfn-lint - a tool to help you check CloudFormation yaml/json templates and other information. This includes checking that the resource's properties are correct or that the configuration information is following best practices.

```
pip install cfn-lint
```

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00015-createawscloud9.png)

16. Check the **cfn-lint** installation is successful using the following command:

```
cfn-lint --version
```

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00016-createawscloud9.png)

17. Install **taskcat**

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00017-createawscloud9.png)


18. We will configure the aws cli to use the current Region.

```
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
export AZS=($(aws ec2 describe-availability-zones --query 'AvailabilityZones[].ZoneName' --output text --region $AWS_REGION))
```

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00018-createawscloud9.png)

19. We will save the configuration information to bash_profile

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00019-createawscloud9.png)

20. We will use the command to check if the Cloud9 IDE is using the IAM Role correctly.

```
aws sts get-caller-identity --query Arn | grep CloudFormation-Role -q && echo "IAM role valid" || echo "IAM role NOT valid"
```
![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00020-createawscloud9.png)