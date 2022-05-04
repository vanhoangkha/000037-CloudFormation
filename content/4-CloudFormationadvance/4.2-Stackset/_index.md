---
title : "Mappings and Stacksets"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 4.2 </b> "
---

#### Mapping
The mapping part in the CloudFormation template makes sense in assigning a key to a set of corresponding values.
For example, if you want to set values ​​based on region, you can create a mapping between the region acting as the key with the specified values ​​located in the region where the template is deployed.
This can be especially useful when deploying AMI installation packages to the global, which is the case where the IDs of the AMIs are different from region to region.

#### StackSets

AWS CloudFormation StackSets extends the functionality of Stacks by allowing you to create, update, or delete Stacks located across multiple Accounts or across multiple Regions with a single operation.

Using an Administrator User, you can define and manage an AWS CloudFormation template, and use this template as the basis for deploying the Stack to the Accounts and Regions you desire.

For example, you can easily set up AWS CloudTrail or AWS Config policies across multiple Accounts with a single StackSet operation. You can also use StackSets to deploy resources to an Account but across multiple Regions.

In this section, we will implement a simple CloudFormation template that will create an EC2 Instance running the web server. We will use the mapping to correctly deploy the Amazon Linux 2 AMI to the selected Region, while using StackSets to configure which Region to deploy this template. For the sake of simplicity, we will use a User with absolute administrative rights to execute, but in fact you can use another account with more limited permissions to deploy StackSets.

For details you can visit the Prerequisites: Granting Permissions for Stack Set Operations page for more information on how to properly configure the two Roles required for deploying StackSets across multiple Accounts.

1. Access the [AWS Management Console](https://aws.amazon.com/console/)

- Find **CloudFormation**
- Select **CloudFormation**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/0001-stackset.png)

2. View created stacks

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/0002-stackset.png)

3. In the **CloudFormation** interface

- Select **Stack**
- Select **Create stack**
- Select **With new resource (standard)**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/0003-stackset.png)

4. In the **Create stack** interface

The CloudFormation template will call two YAML files written by AWS to help define the IAM roles needed for StackSet deployment.
- Create a file **mapping_stackset_iam.yaml**
- Then copy and paste the content of this code into the file and save it:

```
AWSTemplateFormatVersion: '2010-09-09'
Description: This CloudFormation StackSet deploys two AWS provided CloudFormation templates that add Administrator and Execution Roles required to use AWSCloudFormationStackSetAdministrationRole
Resources:
  AWSCloudFormationStackSetAdministrationRole:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: https://s3.amazonaws.com/cloudformation-stackset-sample-templates-us-east-1/AWSCloudFormationStackSetAdministrationRole.yml 
      TimeoutInMinutes: '3'
  AWSCloudFormationStackSetExecutionRole:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: https://s3.amazonaws.com/cloudformation-stackset-sample-templates-us-east-1/AWSCloudFormationStackSetExecutionRole.yml 
      TimeoutInMinutes: '3'
      Parameters:
        AdministratorAccountId : !Ref 'AccountID'
Parameters:
  AccountID:
      Type: String
      Description: Your AWS Account ID
      MaxLength: 12
      MinLength: 12
```

- You can refer to the Role configuration templates here:

[1. AWSCloudFormationStackSetAdministrationRole](https://s3.amazonaws.com/cloudformation-stackset-sample-templates-us-east-1/AWSCloudFormationStackSetAdministrationRole.yml)

[2. AWSCloudFormationStackSetExecutionRole](https://s3.amazonaws.com/cloudformation-stackset-sample-templates-us-east-1/AWSCloudFormationStackSetExecutionRole.yml)

- Select **Template is ready**
- Select **Upload a template file**
- Select **Choose file**
- Select **mapping_stackset_iam.yaml**
- Select **Next**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/0004-stackset.png)

5. In the **Specify stack details** interface

- **Stack name**, enter ```mapping-stacksets-iam```
- **AccountID**, enter your account id

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/0005-stackset.png)

6. Select **Next**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/0006-stackset.png)

7. Trong giao diện **Create stack**

- Chọn **I acknowledge that AWS CloudFormation might create IAM resources with custom names**
- Chọn **I acknowledge that AWS CloudFormation might require the following capability: CAPABILITY_AUTO_EXPAND**
- Chọn **Create stack**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/0007-stackset.png)

8. Khoảng vài phút sau, 2 CloudFormation template đã được tạo thành công cùng với Stack.

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/0008-stackset.png)

9. Trong giao diện **CloudFormation**

- Chọn **Stackset**
- Xem giao diện **Stackset**
- Chọn **Stackset**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/0009-stackset.png)

10. Trong giao diện **Choose a template**

- Bước **Permissions**, chọn **IAM role name**
- Chọn **IAM execution role name**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00010-stackset.png)

11. In the **Prerequisite** interface

- Select **Template is ready**
- **Template source**, select **Upload a template file**
- Select **Choose file**
- Select **mapping_stackset_ec2.yml**
- Select **Next**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00011-stackset.png)

12. In the **Specify StackSet details** interface

- **StackSet name**, enter ```mapping-stacksets-ec2```
- Select **Next**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00012-stackset.png)

13. Select **Next**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00013-stackset.png)

14. In the **Set deployment options** interface

- **Add stacks to stack set**, select **Deploy new stack**
- **Account**, select **Deploy stack in accounts**
- **Account numbers**, enter your account number

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00014-stackset.png)

15. In the **Specify region** interface

- Select the regions you want to deploy

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00015-stackset.png)

16. In the **Deployment options** interface

- Select **Next**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00016-stackset.png)

17. Select **Submit**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00017-stackset.png)

18. StackSet initialization process will take about 5 minutes until you see the screen similar to below.

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00018-stackset.png)

19. Both Regions have been successfully deployed StackSet

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00019-stackset.png)

20. In the **CloudFormation** interface

- Select Region **Europe (Frankfurt)**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00020-stackset.png)

21. In Region **Europe (Frankfurt)**

- Select **CloudFormation**
- Select **Stack**
- Access the CloudFormation console on Region Frankurt - which is one of the Regions we have selected to deploy the StackSet - and see that a new Stack has been created. This proves our StackSet has been deployed successfully.

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00021-stackset.png)

22. In the **CloudFormation** interface

- Select the Stack name, it will lead us to the detailed information page.
- Switch to the Output tab, we will see website results and links

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00022-stackset.png)

23. Select the link and paste it in the browser

- The results obtained are exactly what we expected. The StackSet implementation has been really successful. If you've made it this far, congratulations!

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00023-stackset.png)