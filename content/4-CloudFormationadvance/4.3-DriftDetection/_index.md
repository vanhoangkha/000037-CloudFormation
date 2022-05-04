---
title : "Drift Detection"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 4.3 </b> "
---

#### Drift Detection

AWS CloudFormation allows you to detect configuration changes in your Stack resources caused by the AWS Management Console, CLI, and SDKs. Drift is the difference between the Stack's expected resource configuration determined by CloudFormation templates and its actual resource configuration on CloudFormation.
This feature helps you better manage Stacks and ensures consistency in resource configurations. For more detailed information on Drift, refer to the AWS Blog.

In this exercise, we will create a CloudFormation Stack and then configure its resources using the AWS Management Console.


1. Access the interface [AWS Management Console](https://aws.amazon.com/console/)

- Find **CloudFormation**
- Select **CloudFormation**

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0001-driftdetection.png)

2. In the **CloudFormation** interface

- Select **Stack**
- Select **Create stack**
- Select **With new resources (standard)**

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0002-driftdetection.png)

3. In the **Create stack** interface

- Create a file **my_cfn_stack.yml**
- Then copy and paste this code and save it:

```
AWSTemplateFormatVersion: "2010-09-09"
Resources: 
  InputQueue: 
    Type: "AWS::SQS::Queue"
    Properties: 
      QueueName: "DriftLab-InputQueue"
      VisibilityTimeout: 30
      RedrivePolicy: 
        deadLetterTargetArn: 
          Fn::GetAtt: 
            - "DeadLetterQueue"
            - "Arn"
        maxReceiveCount: 5
  DeadLetterQueue: 
    Type: "AWS::SQS::Queue"
    Properties: 
      QueueName: "DriftLab-ErrorQueue"
```

- Select **Template is ready**
- Select **Upload a template file**
- Select **Choose file**
- Select **my_cfn_stack.yml**
- Select **Next**

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0003-driftdetection.png)

4. In the **Specify stack details** interface

- **Stack name**, enter ```drift-lab-with-sqs```
- Select **Next**

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0004-driftdetection.png)

5. Select **Next**

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0005-driftdetection.png)

6. Select **Create stack**

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0006-driftdetection.png)

7. In the **CloudFormation** interface

- Select **Stack details**
- Select the stack just created
- Select **Event** to see the initialization events
- Status changed to **CREATE_COMPLETE** is initialization successful

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0007-driftdetection.png)

8. In the **CloudFormation** interface

- Select **Stack details**
- Select the stack just created
- Select **Resources**
- View newly created resources

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0008-driftdetection.png)

9. In the **CloudFormation** interface

- Select the stack just created
- Select **Stack actions**
- Select **Detect drift**

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0009-driftdetection.png)

10. In the **CloudFormation** interface

- Select the stack just created
- Select **Stack info**
- See **Drift status** switch to **IN_SYNC**

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00010-driftdetection.png)

11. Access the interface [AWS Management Console](https://aws.amazon.com/console/)

- Find **Simple Queue Service**
- Select **Simple Queue Service**

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00011-driftdetection.png)

12. In the **Amazon SQS** interface

- Select **DriftLab-InputQueue**
- Select **Edit**

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00012-driftdetection.png)

13. In the *Configuration** interface

- *Visibility timeout**, enter ```50```
- **Delivery delay**, enter ```120```

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00013-driftdetection.png)

14. Select **Save**

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00014-driftdetection.png)

15. Successfully edited **AWS SQS** interface

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00015-driftdetection.png)

16. In the **CloudFormation** interface

- Select **Stack details**
- Select **drift-lab-with-sqs**
- Select **Stack info**
- Select **Stack actions**
- Select **Detect drift**

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00016-driftdetection.png)

17. In the **CloudFormation** interface

- Select **Stack details**
- Select **Stack info**
- See **Drift status** switch to **DRIFTED**

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00017-driftdetection.png)

18. In the **CloudFormation** interface

- Select **Stack details**
- Select **Stack info**
- Select **Stack actions**
- Select **View drift results**

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00018-driftdetection.png)

19. In the **Drifts** interface

- View **Drift status**
- See **Resource drift status**, ```InputQueue``` change **Drift status** to **MODIFIED**

![Drift Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00019-driftdetection.png)