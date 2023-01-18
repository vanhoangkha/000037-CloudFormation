---
title : "Drift  Detection"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 4.3 </b> "
---

#### Drift  Detection

AWS CloudFormation cho phép bạn phát hiện sự thay đổi cấu hình về mặt tài nguyên của Stack gây ra bởi AWS Management Console, CLI, và SDKs. Drift là sự khác biệt giữa cấu hình tài nguyên dự kiến của Stack xác định bởi các CloudFormation template với cấu hình tài nguyên thực tế của nó trên CloudFormation.
Tính năng này giúp bạn quản lý các Stack tốt hơn và đảm bảo tính nhất quán trong các cấu hình tài nguyên. Để có thêm thông tin chi tiết về Drift, hãy tham khảo Blog AWS.

Trong bài thực hành lần này, chúng ta sẽ tạo một CloudFormation Stack rồi thay đổi cấu hình tài nguyên của nó sử dụng AWS Management Console.


1. Truy cập vào giao diện [AWS Management Console](https://aws.amazon.com/console/)

- Tìm **CloudFormation**
- Chọn **CloudFormation**

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0001-driftdetection.png)

2. Trong giao diện **CloudFormation**

- Chọn **Stack**
- Chọn **Create stack**
- Chọn **With new resources (standard)**

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0002-driftdetection.png)

3. Trong giao diện **Create stack**

- Tạo một file **my_cfn_stack.yml**
- Sau đó sao chép và dán đoạn mã này đồng thời lưu lại:

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

- Chọn **Template is ready**
- Chọn **Upload a template file**
- Chọn **Choose file**
- Chọn **my_cfn_stack.yml**
- Chọn **Next**

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0003-driftdetection.png)

4. Trong giao diện **Specify stack details**

- **Stack name**, nhập ```drift-lab-with-sqs```
- Chọn **Next**

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0004-driftdetection.png)

5. Chọn **Next**

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0005-driftdetection.png)

6. Chọn **Submit**

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0006-driftdetection.png)

7. Trong giao diện **CloudFormation**

- Chọn **Stack details**
- Chọn stack vừa tạo
- Chọn **Event** xem các event khởi tạo
- Trạng thái chuyển sang **CREATE_COMPLETE** là khởi tạo thành công

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0007-driftdetection.png)

8. Trong giao diện **CloudFormation**

- Chọn **Stack details**
- Chọn stack vừa tạo
- Chọn **Resources**
- Xem các resource vừa tạo 

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0008-driftdetection.png)

9. Trong giao diện **CloudFormation**

- Chọn stack vừa tạo
- Chọn **Stack actions**
- Chọn **Detect drift**

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/0009-driftdetection.png)

10. Trong giao diện **CloudFormation**

- Chọn stack vừa tạo
- Chọn **Stack info**
- Xem **Drift status** chuyển sang **IN_SYNC**

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00010-driftdetection.png)

11. Truy cập vào giao diện [AWS Management Console](https://aws.amazon.com/console/)

- Tìm **Simple Queue Service**
- Chọn **Simple Queue Service**

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00011-driftdetection.png)

12. Trong giao diện **Amazon SQS**

- Chọn **DriftLab-InputQueue**
- Chọn **Edit**

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00012-driftdetection.png)

13. Trong giao diện **Configuration**

- **Visibility timeout**, nhập ```50```
- **Delivery delay**, nhập ```120```

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00013-driftdetection.png)

14. Chọn **Save**

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00014-driftdetection.png)

15. Giao diện **AWS SQS** edit thành công

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00015-driftdetection.png)

16. Trong giao diện **CloudFormation**

- Chọn **Stack details**
- Chọn **drift-lab-with-sqs**
- Chọn **Stack info**
- Chọn **Stack actions**
- Chọn **Detect drift**

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00016-driftdetection.png)

17. Trong giao diện **CloudFormation**

- Chọn **Stack details**
- Chọn **Stack info**
- Xem **Drift status** chuyển sang **DRIFTED**

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00017-driftdetection.png)

18. Trong giao diện **CloudFormation**

- Chọn **Stack details**
- Chọn **Stack info**
- Chọn **Stack actions**
- Chọn **View drift results**

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00018-driftdetection.png)

19. Trong giao diện **Drifts**

- Xem **Drift status** 
- Xem **Resource drift status**, ```InputQueue``` chuyển **Drift status** sang **MODIFIED** 

![Drift  Detection](/images/4-CloudFormationadvance/4.3-DriftDetection/00019-driftdetection.png)