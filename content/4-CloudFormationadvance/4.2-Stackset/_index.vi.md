---
title : "Ánh xạ và Stacksets"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 4.2 </b> "
---

#### Ánh xạ
Phần ánh xạ trong CloudFormation template có ý nghĩa trong việc gán một key với một tập các giá trị tương ứng.
Ví dụ: nếu bạn muốn thiết lập các giá trị dựa theo region, bạn có thể tạo ánh xạ giữa region đóng vai trò là key với các giá trị chỉ định nằm trong region mà template được triển khai.
Điều này có thể đặc biệt hữu ích khi triển khai các gói cài đặt AMI cho global, đó là trường hợp mà ID của các AMI là khác nhau giữa các khu vực.

#### StackSets

AWS CloudFormation StackSets giúp mở rộng chức năng của Stack bằng cách cho phép bạn tạo, cập nhật hoặc xóa Stack nằm trên trên nhiều Account hoặc trên nhiều Region chỉ với một thao tác duy nhất.

Sử dụng User có quyền quản trị, bạn có thể định nghĩa và quản lý một AWS CloudFormation template, đồng thời sử dụng template này làm cơ sở để triển khai Stack lên các Account và Region mà ta mong muốn.

Ví dụ: bạn có thể dễ dàng thiết lập chính sách AWS CloudTrail hoặc AWS Config trên nhiều Account chỉ bằng một thao tác với StackSet. Bạn cũng có thể sử dụng StackSets để triển khai tài nguyên cho một Account nhưng nằm trên nhiều Region.

Ở phần này, chúng ta sẽ triển khai một CloudFormation template đơn giản giúp tạo ta EC2 Instance chạy web server. Chúng ta sẽ sử dụng ánh xạ để triển khai chính xác Amazon Linux 2 AMI cho Region đã chọn, trong khi sử dụng StackSets để cấu hình Region nào sẽ triển khai template này. Để cho đơn giản, chúng ta sẽ sử dụng User có quyền quản trị tuyệt đối để thực thi, tuy nhiên thực tế bạn có thể sử dụng tài khoản khác giới hạn về quyền hơn để triển khai StackSets.

Chi tiết bạn có thể truy cập trang Prerequisites: Granting Permissions for Stack Set Operations để biết thêm thông tin về cách cấu hình đúng hai Role cần thiết cho việc triển khai StackSets trên nhiều Account.

1. Truy cập giao diện [AWS Management Console](https://aws.amazon.com/console/)

- Tìm **CloudFormation**
- Chọn **CloudFormation**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/0001-stackset.png)

2. Xem các stack đã tạo

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/0002-stackset.png)

3. Trong giao diện **CloudFormation**

- Chọn **Stack**
- Chọn **Create stack**
- Chọn **With new resource (standard)**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/0003-stackset.png)

4. Trong giao diện **Create stack**

- CloudFormation template sẽ gọi tới 2 file YAML được viết bởi AWS giúp xác định IAM roles cần thiết cho việc triển khai StackSet.
- Tạo một file **mapping_stackset_iam.yaml**
- Sau đó sao chép và dán nội dụng mã này vào file đồng thời lưu lại:

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

- Bạn có thể tham khảo các template cấu hình Role ở đây:

	[1. AWSCloudFormationStackSetAdministrationRole](https://s3.amazonaws.com/cloudformation-stackset-sample-templates-us-east-1/AWSCloudFormationStackSetAdministrationRole.yml)

	[2. AWSCloudFormationStackSetExecutionRole](https://s3.amazonaws.com/cloudformation-stackset-sample-templates-us-east-1/AWSCloudFormationStackSetExecutionRole.yml)

- Chọn **Template is ready**
- Chọn **Upload a template file**
- Chọn **Choose file**
- Chọn **mapping_stackset_iam.yaml**
- Chọn **Next**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/0004-stackset.png)

5. Trong giao diện **Specify stack details**

- **Stack name**, nhập ```mapping-stacksets-iam```
- **AccountID**, nhập account id của bạn

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/0005-stackset.png)

6. Chọn **Next**

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

11. Trong giao diện **Prerequisite**

- Chọn **Template is ready**
- **Template source**, chọn **Upload a template file**
- Chọn **Choose file**
- Chọn **mapping_stackset_ec2.yml**
- Chọn **Next**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00011-stackset.png)

12. Trong giao diện **Specify StackSet details**

- **StackSet name**, nhập ```mapping-stacksets-ec2```
- Chọn **Next**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00012-stackset.png)

13. Chọn **Next**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00013-stackset.png)

14. Trong giao diện **Set deployment options**

- **Add stacks to stack set**, chọn **Deploy new stack**
- **Account**, chọn **Deploy stack in accounts**
- **Account numbers**, nhập account number của bạn

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00014-stackset.png)

15.  Trong giao diện **Specify region**

- Chọn các region bạn muốn triển khai 

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00015-stackset.png)

16. Trong giao diện **Deployment options**

- Chọn **Next**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00016-stackset.png)

17. Chọn **Submit**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00017-stackset.png)

18. Tiến trình khởi tạo StackSet sẽ mất khoảng 5 phút cho đến khi thấy được màn hình tương tự bên dưới. 

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00018-stackset.png)

19. Cả 2 Region đều được triển khai StackSet thành công

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00019-stackset.png)

20. Trong giao diện **CloudFormation**

- Chọn Region **Europe (Frankfurt)**

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00020-stackset.png)

21. Trong Region **Europe (Frankfurt)**

- Chọn **CloudFormation**
- Chọn **Stack**
- Truy cập CloudFormation console trên Region Frankurt - là một trong những Region ta đã lựa chọn để triển khai StackSet - thì thấy một Stack mới đã được tạo ra. Điều này chứng tỏ StackSet của chúng ta đã được triển khai thành công.

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00021-stackset.png)

22. Trong giao diện **CloudFormation**

- Chọn tên Stack, nó sẽ dẫn ta tới trang thông tin chi tiết. 
- Chuyển sang tab Output, ta sẽ thấy kết quả website và đường dẫn

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00022-stackset.png)

23. Chọn đường dẫn và dán vào trình duyệt 

- Kết quả thu được đúng với những gì chúng ta mong đợi. Việc triển khai StackSet đã thực sự thành công. Nếu các bạn đã đi được tới bước này thì xin chúc mừng các bạn!

![Stacksets](/images/4-CloudFormationadvance/4.2-Stackset/00023-stackset.png)

