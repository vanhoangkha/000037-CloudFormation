+++
title = "AWS CloudFormation Cơ bản"
date = 2020
weight = 1
chapter = true
pre = "<b>1. </b>"
+++

# AWS CloudFormation Cơ bản

{{% notice tip %}}
Lưu ý: trình duyệt đối với Windows như Chrome, Firefox, hoặc Edge phải là phiên bản mới nhất; cũng như vậy với  Apple Safari trên macOS
{{% /notice %}}

### Khởi tạo môi trường AWS Cloud9

1 - Lựa chọn Region có tên **EU (Ireland)** với ID là **eu-west-1**.  
Workshop lần này yêu cầu chúng ta phải chọn chính xác Region bởi không phải tất cả các regions đều có số lượng dịch vụ giống nhau, một dịch vụ có thể chạy ở region này nhưng chưa chắc nó đã chạy ở region khác. Cloud9 là một dịch vụ như vậy.
![Security Hub](../../../images/1.png?width=15pc)
2 - Truy cập trang chủ dịch vụ [AWS Cloud9](https://ap-northeast-1.console.aws.amazon.com/cloud9/home/product). Bấm **Create Environment**. 
![Security Hub](../../../images/2.png?width=90pc)
3 - Nhập tên Environment Name ***ASG-Cloud9-Workshop*** rồi bấm **Next Step**
![Security Hub](../../../images/3.png?width=90pc) 
4 - Phần **Environment type** ở đây chúng ta có 3 lựa chọn 
 - **Create a new EC2 instance for environment (direct access)**: EC2 Instance được khởi tạo cùng với Cloud9 environment. Instance được truy cập qua Cloud9 IDE sử dụng phương thức SSH. 
 - **Create a new no-ingress EC2 instance for environment (access via Systems Manager)**: EC2 Instance cũng được khởi tạo cùng với Cloud9 environment và Instance có thể được truy cập qua System Manager.
 - **Create and run in remote server (SSH connection)**: EC2 Instance đã có sẵn, Cloud9 eviroment thiết lập môi trường để đủ điều kiện truy cập EC2 Instance.  

Trong phạm vi bài giới thiệu về dịch vụ CloudFormation hôm nay, để đơn giản và tập trung vào nội dung chính chúng ta sẽ chọn option thứ nhất - EC2 và Cloud9 được khởi tạo cùng lúc và có thể truy cập Instance qua SSH.  
Các nội dung cấu hình còn lại ta để mặc định với: 
 - **Cost-saving setting**: sau 30' nếu EC2 Instance không có tiến trình nào được chạy, Cloud9 sẽ stop Instance. 
 - **IAM Role**: ***AWSServiceRoleForAWSCloud9*** - là service-linked role được tạo sẵn bởi AwS và gắn với dịch vụ Cloud9. 
![Security Hub](../../../images/4.png?width=90pc)

5 - Thực hiện review lần cuối rồi bấm **Create Environment**
![Security Hub](../../../images/5.png?width=90pc)
Ngay lập tức màn hình chuyển sang thông báo đang thiết lập môi trường và mất khoảng 5' để hoàn thành. 
![Security Hub](../../../images/6.png?width=90pc) 

6 - Kết thúc quá trình thiết lập, màn hình cho ta thấy Cloud9 IDE đã truy cập thành công EC2 Instance. Bấm vào biểu tượng **Cloud9** ở góc trái phía bên, rồi chọn **Preference**. 
![Security Hub](../../../images/7.png?width=90pc)
7 - Bấm tiếp vào **AWS Settings**, rồi chọn **Turn off temporary Credentials**. Tiếp đó bấm **X** trên tab **Preferences** để đóng cửa sổ cấu hình lại.
![Security Hub](../../../images/8.png?width=90pc)
8 - Trở về của sổ terminal trên Cloud9, thực hiện cài đặt tool **cfn-lint** - là công cụ giúp bạn kiểm tra CloudFormation yaml/json templates và các thông tin khác. Bao gồm kiểm tra các thuộc tính của tài nguyên đã chính xác hay chưa hoặc thông tin cấu hình đã theo best practices hay chưa.  
Để cài đặt, dùng lệnh 
    ```python
    sudo pip install cfn-lint
    ```  

Và giờ ta đã sẵn sàng để chuyển sang bước tiếp theo, thực hiện tạo CloudFormation template. 


### Xây dựng CloudFormation template
Một trong những chìa khóa để làm việc hiệu quả với CloudFormation đó là phải biết được cách sử dụng bảng tham chiếu tài nguyên ở trang [AWS Resource and Property Types Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html). 
Tại đây bạn có thể tìm thấy một danh sách thông tin và định nghĩa về các tài nguyên và các thuộc tính.  

Trước khi bắt đầu xây dựng template CloudFormation template, truy cập **Cloud9** >> bấm **File** >> **New File** để tạo một file mới dùng để lưu nội dung mã code tạo template

1 - Cấu trúc của một CloudFormation template  
Một CloudFormation template thường bao gồm 9 phần, chi tiết về chúng bạn có thể xem ở [link](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html). 
Trong phạm vi workshop, chúng ta chỉ quan tâm tới 5 thành phần chính sau đây:
- **AWSTemplateFormatVersion** (tùy chọn): chỉ ra phiên bản của template. Bản mới nhất là 2010-09-09 và mặc dù đã ra đời cách đây khá lâu vẫn được sử dụng cho tới thời điểm hiện tại.
- **Description** (tùy chọn) mô tả thông tin liên quan tới template.
- **Parameters** (tùy chọn): cho phép bạn bổ sung các giá trị tùy chọn vào template mỗi lần ta cấn tạo hoặc update stack.
- **Resources** (bắt buộc): chỉ rõ những tài nguyên AWS nào bạn muốn đưa vào stack để deploy, ví dụ như Amazon EC2 instance hoặc một Amazon S3 bucket.
- **Outputs** (tùy chọn) xác định những giá trị đầu ra mà bạn có thể import vào những stack khác (để tạo *cross-stack references*), hoặc làm nội dung phản hồi cho stack , hoặc dể hiển thị trên AWS CloudFormation console.

2 - Template Parameters  
Với mỗi parameter chúng ta phải chỉ rõ tên và loại cho parameter. Các loại parameter được hỗ trợ bao gồm: *String*, *Number*, *CommaDelimitedList*, *List<Number>*, *AWS-Specific Parameter* và *SSM Parameter*. 
Bên dưới là một số parameter chúng ta sử dụng trong workshop. Chúng bao gồm
- **EC2InstanceType** - xác định loại Instance sẽ được sử dụng trong template.
- **LatestAmiId** -  cho phép tự động lấy AMI ID mới nhất và đưa nó vào template.
- **SubnetID** - liệt kê tất cả các Subnet IDs khả dụng có trong region.
- **SourceLocation** - quy định cách sử dụng regex trong thuộc tính AllowedPattern để đảm bảo giá trị nhập vào là thích hợp, cùng với các giá trị MinLength và MaxLength.
- **VPCID** - liệt kê các VPCs có sẵn trong region.  

Thực hiện dán nội dung dưới đây vào cửa sổ **New File**
```python
AWSTemplateFormatVersion: "2010-09-09"
Description: "Deploy Single EC2 Linux Instance as part of MGT312 Workshop"
Parameters:
  EC2InstanceType:
    AllowedValues:
      - t3.nano
      - t3.micro
      - t3.small
      - t3.medium
      - t3.large
      - t3.xlarge
      - t3.2xlarge
      - m5.large
      - m5.xlarge
      - m5.2xlarge
    Default: t3.small
    Description: Amazon EC2 instance type
    Type: String
  LatestAmiId:
    Type: 'AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>'
    Default: "/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2"
  SubnetID:
    Description: ID of a Subnet.
    Type: AWS::EC2::Subnet::Id
  SourceLocation:
    Description : The CIDR IP address range that can be used to RDP to the EC2 instances
    Type: String
    MinLength: 9
    MaxLength: 18
    Default: 0.0.0.0/0
    AllowedPattern: "(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})/(\\d{1,2})"
    ConstraintDescription: must be a valid IP CIDR range of the form x.x.x.x/x.
  VPCID:
    Description: ID of the target VPC (e.g., vpc-0343606e).
    Type: AWS::EC2::VPC::Id
```
3 - Resource: Security Group  
Và chúng ta đã thiết lập xong các tham số để có thể tái sử dụng CloudFormaton ở những lần sau. Giờ chúng ta sẽ đi xác định những thành phần bắt buộc, đó là Tài nguyên.   
Cần lưu ý rằng việc sử dụng thành thạo tài liệu [AWS Resource and Property Types Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html) đóng vai trò rất quan trọng trong việc tạo ra CloudFormation template.  
Tài nguyên đầu tiên chúng ta thêm vào là Security Group với quy tắc cho phép lưu lượng truy cập trên cổng 80. 
Ở đây chúng ta sử dụng kỹ thuật *Tham chiếu Hàm Nội tại *(*Intrinsic Function Ref*) để trả về giá trị của tham số *VPCID*.   
AWS CloudFormation cung cấp một số chức năng tích hợp giúp bạn quản lý các Stack và sử dụng các hàm nội tại trong template để gán giá trị cho các thuộc tính chỉ khả dụng tại thời điểm runtime.  
Thực hiện sao chép nội dung sau đây rồi dán nối tiếp vào đoạn code trong cửa sổ **New File** ở bước trên: 
```python
Resources:
  EC2InstanceSG:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: EC2 Instance Security Group
      VpcId: !Ref 'VPCID'
      SecurityGroupIngress:
      - IpProtocol: tcp
        FromPort: 80
        ToPort: 80
        CidrIp: !Ref SourceLocation
```
4 - Resource: Instance Role  
Ở bước này chúng ta sẽ đi tạo Role dùng để gán cho EC2 Instance, cho phép nó gửi thông tin giám sát và giao tiếp với *AWS Systems Manager Service* (*SSM*). Cùng với đó một InstanceProfile cũng được tạo ra và tham chiếu tới Instance Role.  
Đoạn code cũng sử dụng kỹ thuật *Hàm Phụ Nội tại* (*Intrinsic Function Sub*) để thay thế các giá trị trong một chuỗi với *Tham số Giả* ([Pseudo parameters](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/pseudo-parameter-reference.html)) như **AWS::Region** và **AWS::Partition**.  
*Tham số Giả* là những tham số được xác định trước. Chúng ta sẽ không khai báo chúng trong template và sử dụng chúng giống như một tham số thông thường.  
Thực hiện sao chép nội dung sau đây rồi dán nối tiếp vào đoạn code trong cửa sổ **New File** ở bước trên: 
```python
  SSMInstanceRole:
    Type : AWS::IAM::Role
    Properties:
      Policies:
        - PolicyDocument:
            Version: '2012-10-17'
            Statement:
              - Action:
                  - s3:GetObject
                Resource:
                  - !Sub 'arn:aws:s3:::aws-ssm-${AWS::Region}/*'
                  - !Sub 'arn:aws:s3:::aws-windows-downloads-${AWS::Region}/*'
                  - !Sub 'arn:aws:s3:::amazon-ssm-${AWS::Region}/*'
                  - !Sub 'arn:aws:s3:::amazon-ssm-packages-${AWS::Region}/*'
                  - !Sub 'arn:aws:s3:::${AWS::Region}-birdwatcher-prod/*'
                  - !Sub 'arn:aws:s3:::patch-baseline-snapshot-${AWS::Region}/*'
                Effect: Allow
          PolicyName: ssm-custom-s3-policy
      Path: /
      ManagedPolicyArns:
        - !Sub 'arn:${AWS::Partition}:iam::aws:policy/AmazonSSMManagedInstanceCore'
        - !Sub 'arn:${AWS::Partition}:iam::aws:policy/CloudWatchAgentServerPolicy'
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
        - Effect: "Allow"
          Principal:
            Service:
            - "ec2.amazonaws.com"
            - "ssm.amazonaws.com"
          Action: "sts:AssumeRole"
  SSMInstanceProfile:
    Type: "AWS::IAM::InstanceProfile"
    Properties:
      Roles:
      - !Ref SSMInstanceRole
```

5 - Resource: EC2 Instance  
Tiếp tục xây dựng template với việc xác định EC2 Instance.   
Ở bước này tất cả các thành phần bao gồm các tài nguyên và thông số đã cấu hình trước đó sẽ được tập hợp lại với nhau. Chúng ta sử dụng kỹ thuật *Tham chiếu Hàm Nội tại* *!Ref* để gán hoặc tạo ra các tài nguyên và giá trị sinh ra tại thời điểm runtime.
Việc sử dụng !Ref cũng ảnh hưởng đến thứ tự tài nguyên được tạo bởi CloudFormation.  
Thực hiện sao chép nội dung sau đây rồi dán nối tiếp vào đoạn code trong cửa sổ **New File** ở bước trên: 
```python
  EC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      ImageId: !Ref LatestAmiId
      InstanceType: !Ref EC2InstanceType
      IamInstanceProfile: !Ref SSMInstanceProfile
      NetworkInterfaces:
        - DeleteOnTermination: true
          DeviceIndex: '0'
          SubnetId: !Ref 'Subnet'
          GroupSet:
            - !Ref EC2InstanceSG
      Tags:
      - Key: "Name"
        Value: "MGMT312-EC2"
```

6 - Outputs
Cuối cùng là phần nội dung Outputs với kết quả trả về là địa chỉ Private IP của Instance - một giá trị thuộc tính của Resource - thông qua sử dụng *Hàm Nội tại GettAtt*.
Tham chiếu tài liệu AWS Resource and Property Types Reference để tìm kiếm thêm thông tin có thể lấy được nhờ hàm GetAtt hoặc !Ref.
Thực hiện sao chép nội dung sau đây rồi dán nối tiếp vào đoạn code trong cửa sổ New File ở bước trên: 
```python
Outputs:
  EC2InstancePrivateIP:
    Value: !GetAtt 'EC2Instance.PrivateIp'
    Description: Private IP for EC2 Instances
```

7 - Kiểm tra lại template lần cuối, rồi thực hiện lưu **New File** với tên ***singleec2instance.yaml*** đặt tại đường dẫn ***/home/ec2-user-environment***
```python
AWSTemplateFormatVersion: "2010-09-09"
Description: "Deploy Single EC2 Linux Instance"
Parameters:
  EC2InstanceType:
    AllowedValues:
      - t3.nano
      - t3.micro
      - t3.small
      - t3.medium
      - t3.large
      - t3.xlarge
      - t3.2xlarge
      - m5.large
      - m5.xlarge
      - m5.2xlarge
    Default: t3.small
    Description: Amazon EC2 instance type
    Type: String
  LatestAmiId:
    Type: 'AWS::SSM::Parameter::Value<AWS::EC2::Image::Id>'
    Default: "/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2"
  SubnetID:
    Description: ID of a Subnet.
    Type: AWS::EC2::Subnet::Id
  SourceLocation:
    Description : The CIDR IP address range that can be used to RDP to the EC2 instances
    Type: String
    MinLength: 9
    MaxLength: 18
    Default: 0.0.0.0/0
    AllowedPattern: "(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})\\.(\\d{1,3})/(\\d{1,2})"
    ConstraintDescription: must be a valid IP CIDR range of the form x.x.x.x/x.
  VPCID:
    Description: ID of the target VPC (e.g., vpc-0343606e).
    Type: AWS::EC2::VPC::Id

Resources:

  EC2InstanceSG:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: EC2 Instance Security Group
      VpcId: !Ref 'VPCID'
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: !Ref SourceLocation

  SSMInstanceRole:
    Type : AWS::IAM::Role
    Properties:
      Policies:
        - PolicyDocument:
            Version: '2012-10-17'
            Statement:
              - Action:
                  - s3:GetObject
                Resource:
                  - !Sub 'arn:aws:s3:::aws-ssm-${AWS::Region}/*'
                  - !Sub 'arn:aws:s3:::aws-windows-downloads-${AWS::Region}/*'
                  - !Sub 'arn:aws:s3:::amazon-ssm-${AWS::Region}/*'
                  - !Sub 'arn:aws:s3:::amazon-ssm-packages-${AWS::Region}/*'
                  - !Sub 'arn:aws:s3:::${AWS::Region}-birdwatcher-prod/*'
                  - !Sub 'arn:aws:s3:::patch-baseline-snapshot-${AWS::Region}/*'
                Effect: Allow
          PolicyName: ssm-custom-s3-policy
      Path: /
      ManagedPolicyArns:
        - !Sub 'arn:${AWS::Partition}:iam::aws:policy/AmazonSSMManagedInstanceCore'
        - !Sub 'arn:${AWS::Partition}:iam::aws:policy/CloudWatchAgentServerPolicy'
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: "Allow"
            Principal:
              Service:
              - "ec2.amazonaws.com"
              - "ssm.amazonaws.com"
            Action: "sts:AssumeRole"

  SSMInstanceProfile:
    Type: "AWS::IAM::InstanceProfile"
    Properties:
      Roles:
        - !Ref SSMInstanceRole

  EC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      ImageId: !Ref LatestAmiId
      InstanceType: !Ref EC2InstanceType
      IamInstanceProfile: !Ref SSMInstanceProfile
      NetworkInterfaces:
        - DeleteOnTermination: true
          DeviceIndex: '0'
          SubnetId: !Ref 'Subnet'
          GroupSet:
            - !Ref EC2InstanceSG
      Tags:
        - Key: "Name"
          Value: "MGMT312-EC2"

Outputs:
  EC2InstancePrivateIP:
    Value: !GetAtt 'EC2Instance.PrivateIp'
    Description: Private IP for EC2 Instances
```

8 - Để giảm bớt gánh nặng trong việc kiểm tra sai sót của mã lệnh táo template thì ta có thể sử dụng một số công cụ tự động kiểm tra để validate cú pháp dùng trong template. Với CloudFormation workshop lần này, chúng ta sử dụng ***cfn-lint***  
Thực hiện chạy lệnh sau đây
```python
ASG-UserAllowCodeCommit:~/environment $ cfn-lint singleec2instance.yaml
sys:1: Warning: Python 2.7 has reached end of life. cfn-lint will end support for python 2.7 on December 31st, 2020.
W2001 Parameter SubnetID not used.
singleec2instance.yaml:22:3

E1012 Ref Subnet not found as a resource or parameter
singleec2instance.yaml:97:11
```
Dễ dàng thấy, đã có lỗi xảy ra với CloudFormation template, cụ thể là dòng 97, chúng ta đã sử dụng sai giá trị ***Subnet***. Công cụ **cfn-lint** thực sự đã làm rất tốt vai trò của mình, phát hiện lỗi sai về cú pháp và thông tin trong template.
![Security Hub](../../../images/9.png?width=90pc)
Thực hiện sửa lại template bằng cách thay giá trị ***Subnet*** thành ***SubnetID***, rồi chạy lại lênh cfn-lint. 
![Security Hub](../../../images/10.png?width=90pc)
Kết quả lệnh chạy thành công và không có lỗi xảy ra, ngoài cảnh báo về python version. Chúng ta có thể ignore chúng bởi đơn giản chúng ta chỉ đang thực hiện một workshop. 

9 - Tạo CloudFormation Stack
Để tạo được CloudFormation Stack, Cloud9 server bắt buộc phải quyền truy cập và sử dụng dịch vụ EC2 và IAM, do vậy cần thực hiện bổ sung thêm policy cho IAM Role đang được attach với Cloud9 server. 
Ví dụ bổ sung thêm các policy cần thiết: 
![Security Hub](../../../images/11.png?width=90pc)
Sau khi Cloud9 server đã có đủ quyền thực hiện việc tạo các Resource với CloudFormation, trên cửa sổ terminal, chạy lệnh 
```python
ASG-UserAllowCodeCommit:~/environment $ aws cloudformation create-stack --stack-name asg-cloudformation-stack --template-body file://singleec2instance.yaml --parameters ParameterKey=SubnetID,ParameterValue=subnet-22680f78 ParameterKey=VPCID,ParameterValue=vpc-ffa44c86 --capabilities CAPABILITY_IAM --region eu-west-1
```
Kết quả hiển thị như bên dưới cho ta biết rằng Cloud9 đã gọi tới dịch vụ CloudFormation để bắt đầu tiến trình khởi tạo Stack
```python
ASG-UserAllowCodeCommit:~/environment $ aws cloudformation create-stack --stack-name asg-cloudformation-stack --template-body file://singleec2instance.yaml --parameters ParameterKey=SubnetID,ParameterValue=subnet-22680f78 ParameterKey=VPCID,ParameterValue=vpc-ffa44c86 --capabilities CAPABILITY_IAM --region eu-west-1
```
Sau khoảng 5', thực hiện kiểm tra tiến trình tạo Stack trên CloudFormation console. 
![Security Hub](../../../images/12.png?width=90pc) 
Và có thể thấy rằng CloudFormation Stack đã được tạo thành công. Bạn có thể kiểm tra tab **Resource** và **Event** để có thêm thông tin chi tiết về quá trình khởi tạo.
![Security Hub](../../../images/13.png?width=90pc) ![Security Hub](../../../images/14.png?width=90pc)

### Tổng kết

Kết thúc bài lab chúng ta có thể đã cảm thấy quen thuộc hơn một chút với AWS CloudFormation thông qua việc tạo một CloudFormation template và biết thêm một vài tính năng cơ bản của CloudFormation, và cách kiểm tra template. Đồng thời chúng ta cũng đã cùng nhau học cách khởi chạy một CloudFormation template sử dụng AWS CLI tạo ra một Stack đơn giản. 
Nếu đã cảm thấy tự tin nắm chắc nội dung phần 1, hãy chuyển sang phần 2 của bài Workshop để tìm hiểu thêm các tính năng tuyệt vời khác của CloudFormation nhé! 