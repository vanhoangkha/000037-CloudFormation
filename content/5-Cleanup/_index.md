---
title : "Clean up resources"
date: 2024-01-01
weight : 5
chapter : false
pre : " <b> 5. </b> "
---

#### Delete Cloud9 Environment

1. Select **Region** containing the environment to delete
2. In the **Cloud9** interface select the environment to delete
3. Select **Delete**
![Clean](/images/5-CleanUpResources/1.png)
4. Verify environment deletion by entering **Delete** and selecting **Delete**
![Clean](/images/5-CleanUpResources/2.png)

#### Delete a stack on the AWS CloudFormation console

1. Go to [AWS CloudFormation](https://console.aws.amazon.com/cloudformation/) page
2. In the **CloudFormation** interface, select **Stack**
3. Select **Stack** to delete
4. Select **Delete**
5. Verify clearing stack
6. Wait a few minutes for the stack to change state to **DELETE_COMPLETE** to be deleted successfully
![Clean](/images/5-CleanUpResources/3.png)

#### Delete a stack set

1. Go to [AWS CloudFormation](https://console.aws.amazon.com/cloudformation/) page
2. Select **StackSets**
3. Select **Stackset** to delete
4. Select **Actions** then select **Delete StackSet**
5. Verify delete stack set and select **Delete StackSet**