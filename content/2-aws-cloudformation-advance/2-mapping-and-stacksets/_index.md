+++
title = "Ánh xạ và StackSets"
date = 2020-04-18T00:38:32+07:00
weight = 2
chapter = false
pre = "<b>2.2. </b>"
+++
---

#### Ánh xạ

Phần ánh xạ trong CloudFormation template có ý nghĩa trong việc gán một key với một tập các giá trị tương ứng.  
Ví dụ: nếu bạn muốn thiết lập các giá trị dựa theo region, bạn có thể tạo ánh xạ giữa region đóng vai trò là key với các giá trị chỉ định nằm trong region mà template được triển khai.  
Điều này có thể đặc biệt hữu ích khi triển khai các gói cài đặt AMI cho global, đó là trường hợp mà ID của các AMI là khác nhau giữa các khu vực.

#### StackSets

[AWS CloudFormation StackSets](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-concepts.html) giúp mở rộng chức năng của Stack bằng cách cho phép bạn tạo, cập nhật hoặc xóa Stack nằm trên trên nhiều Account hoặc trên nhiều Region chỉ với một thao tác duy nhất.  
Sử dụng User có quyền quản trị, bạn có thể định nghĩa và quản lý một AWS CloudFormation template, đồng thời sử dụng template này làm cơ sở để triển khai Stack lên các Account và Region mà ta mong muốn.  
Ví dụ: bạn có thể dễ dàng thiết lập chính sách AWS CloudTrail hoặc AWS Config trên nhiều Account chỉ bằng một thao tác với StackSet.  Bạn cũng có thể sử dụng StackSets để triển khai tài nguyên cho một Account nhưng nằm trên nhiều Region.  
Ở phần này, chúng ta sẽ triển khai một CloudFormation template đơn giản giúp tạo ta EC2 Instance chạy web server. Chúng ta sẽ sử dụng ánh xạ để triển khai chính xác Amazon Linux 2 AMI cho Region đã chọn, trong khi sử dụng StackSets để cấu hình Region nào sẽ triển khai template này.
Để cho đơn giản, chúng ta sẽ sử dụng User có quyền quản trị tuyệt đối để thực thi, tuy nhiên thực tế bạn có thể sử dụng tài khoản khác giới hạn về quyền hơn để triển khai StackSets.  
Chi tiết bạn có thể truy cập trang [Prerequisites: Granting Permissions for Stack Set Operations](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/stacksets-prereqs.html) để biết thêm thông tin về cách cấu hình đúng hai Role cần thiết cho việc triển khai StackSets trên nhiều Account.

## Triển khai StackSet
1 - Truy cập CloudFormation console từ **N. Virginia** region rồi bấm **Create Stack**
2 - Chọn **Upload a template file** rồi upload file **mapping_stacksets_iam.yml** như attach bên dưới rồi bấm **Next**
{{% attachments pattern="mapping_stacksets_iam.yml" /%}} 
![Security Hub](../../../images/28.png?width=90pc)  
CloudFormation template sẽ gọi tới 2 file YAML được viết bởi AWS giúp xác định IAM roles cần thiết cho việc triển khai StackSet.
```python
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
Bạn có thể tham khảo các template cấu hình Role ở đây:
[AWSCloudFormationStackSetAdministrationRole](https://s3.amazonaws.com/cloudformation-stackset-sample-templates-us-east-1/AWSCloudFormationStackSetAdministrationRole.yml)
[AWSCloudFormationStackSetExecutionRole](https://s3.amazonaws.com/cloudformation-stackset-sample-templates-us-east-1/AWSCloudFormationStackSetExecutionRole.yml)

3 - Nhập tên cho CloudFormation stack là ***mapping-stacksets-iam***.  
4 - Chỉ rõ AccountID của AWS account của bạn rồi bấm Next.
![Security Hub](../../../images/29.png?width=90pc)
5 - Tiếp tục bấm **Next** ở bước tiếp theo, cuối cùng bấm **Create Stack** để bắt đầu tạo Stack  
6 - Khoảng vài phút sau, 2 CloudFormation template đã được tạo thành công cùng với Stack. 
![Security Hub](../../../images/30.png?width=90pc)
7 - Trở lại CloudFormation console, cuộn xuống một chút để thấy tính năng **Create StackSet**, rồi bấm **Create StackSet** >> chọn upload file **mapping_stacksets_ec2.yml** rồi bấm **Next**
{{% attachments pattern="mapping_stacksets_ec2.yml" /%}} ![Security Hub](../../../images/31.png?width=90pc)

8 - Nhập tên StackSet là ***mapping-stacksets-ec2*** sau đó bấm **Next**
![Security Hub](../../../images/32.png?width=90pc)
9 - Trong phần **Permission**, lựa chọn **Self-service permissions**, rồi chọn ***AWSCloudFormationStackSetAdministrationRole*** làm **IAM role name**. Mặc định ***AWSCloudFormationStackSetExecutionRole*** cũng đã được thiết lập sẵn làm giá trị cho **IAM execution role name**
![Security Hub](../../../images/33.png?width=90pc)
10 - Ở nội dung **Specify Accounts**, chọn **Deploy stacks in accounts** rồi nhập *AWS AccoundId* của bạn. Phần **Specify regions**, lựa chọn những Region bạn muốn triển khai StackSet lên trên nó. Cuối cùng bấm **Next** >> **Next** >> **Submit**
![Security Hub](../../../images/34.png?width=90pc)
11 - Tiến trình khởi tạo StackSet sẽ mất khoảng 5' cho đến khi thấy được màn hình tương tự bên dưới. Cả 3 Region đều được triển khai StackSet thành công
![Security Hub](../../../images/35.png?width=90pc) 
12 - Truy cập CloudFormation console trên Region **Frankurt** - là một trong những Region ta đã lựa chọn để triển khai StackSet - thì thấy một Stack mới đã được tạo ra. Điều này chứng tỏ StackSet của chúng ta đã được triển khai thành công.
![Security Hub](../../../images/36.png?width=90pc)
13 - Bấm vào tên Stack, nó sẽ dẫn ta tới trang thông tin chi tiết. Chuyển sang tab **Output**, rồi bấm vào đường link website bên dưới
![Security Hub](../../../images/37.png?width=90pc) 
14 - Kết quả thu được đúng với những gì chúng ta mong đợi. Việc triển khai StackSet đã thực sự thành công. Nếu các bạn đã đi được tới bước này thì xin chúc mừng các bạn!
![Security Hub](../../../images/38.png?width=90pc)