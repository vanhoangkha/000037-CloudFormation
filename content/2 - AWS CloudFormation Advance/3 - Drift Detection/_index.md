+++
title = "Lab 3: Drift Dectection"
date = 2020-04-18T00:38:32+07:00
weight = 3
chapter = true
pre = "<b>2.3. </b>"
+++

# Lab 3: Drift Dectection

AWS CloudFormation cho phép bạn phát hiện sự thay đổi cấu hình về mặt tài nguyên của Stack gây ra bởi AWS Management Console, CLI, và SDKs.
Drift là sự khác biệt giữa cấu hình tài nguyên dự kiến của Stack xác định bởi các CloudFormation template với cấu hình tài nguyên thực tế của nó trên CloudFormation.  
Tính năng này giúp bạn quản lý các Stack tốt hơn và đảm bảo tính nhất quán trong các cấu hình tài nguyên. Để có thêm thông tin chi tiết về Drift, hãy tham khảo [Blog AWS](https://aws.amazon.com/blogs/aws/new-cloudformation-drift-detection).

Trong bài thực hành lần này, chúng ta sẽ tạo một CloudFormation Stack rồi thay đổi cấu hình tài nguyên của nó sử dụng AWS Management Console. 

1 - Truy cập CloudFormation console từ **N. Virginia** region rồi bấm **Create Stack**  
2 - Chọn Upload a template file rồi upload file **my_cfn_stack.yml** như attach bên dưới rồi bấm **Next**
{{% attachments pattern="my_cfn_stack.yml" /%}}
![Security Hub](../../../images/39.png?width=90pc)
Template của chúng ta định nghĩa 2 SQS Queues bao gồm 1 Input queue và  1 Error queue.
```python
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
3 - Nhập tên cho Stack là ***drift-lab-with-sqs*** rồi bấm **Next** >> **Next** rồi bấm **Create** để bắt đầu tạo Stack
![Security Hub](../../../images/40.png?width=90pc)
4 - Chờ đến khi Stack hoàn thành việc khởi tạo, bấm Stack Action ở góc phải trên màn hình, rồi bấm Detect drift
![Security Hub](../../../images/41.png?width=90pc)
5 - Tiến trình Detect drift sẽ được khởi chạy ngay lập tức, trở lại tab Stack Info ta sẽ thấy trạng thái Drift status là **IN_SYNC** như hình bên dưới, ý nghĩa là tiến trình Detect drift đã hoàn thành
![Security Hub](../../../images/42.png?width=90pc)
Giữ màn hình thông tin Stack info để ta có thể liên tục kiểm tra trạng thái Drift status.  
6 - Truy cập **Simple Queue Service Console**, tìm tới **DriftLab-InputQueue** rồi bấm **Edit**.
![Security Hub](../../../images/43.png?width=90pc)
7 - Thực hiện các thay đổi sau đối với Queue:
- Default Visibility Timeout: 50.
- Delivery Delay: 120.
![Security Hub](../../../images/44.png?width=90pc)
8 - Trở lại cửa sổ chứa tab **Stack Infor**, rồi thực hiện lại Detection drift. Lúc này kết quả cho ta thấy trạng thái Drift status trở thành **DRIFTED**. 
![Security Hub](../../../images/45.png?width=90pc) 
9 - Bấm vào **Stack Action** >> chọn **View Drift Result**, ta có thể thấy rằng nguyên nhân do có sự chỉnh sửa cấu hình của InputQueue
![Security Hub](../../../images/46.png?width=90pc)