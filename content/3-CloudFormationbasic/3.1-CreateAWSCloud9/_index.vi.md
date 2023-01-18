---
title : "Tạo Workspace"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
pre : " <b> 3.1 </b> "
---
#### Tạo Workspace
1. Truy cập vào giao diện [AWS Management Console](https://aws.amazon.com/console/)

- Tìm **Cloud9**
- Chọn **Cloud9**

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0001-createawscloud9.png)

2. Trong giao diện **AWS Cloud9**

- Chọn **Create environment**

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0002-createawscloud9.png)

3. Trong giao diện **Create environment**

- **Name**, nhập ```ASG-Cloud9-Workshop```

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0003-createawscloud9.png)

4. Trong phần tiếp theo

- **Create a new EC2 instance for environment (direct access)**: EC2 Instance được khởi tạo cùng với Cloud9 environment. Instance được truy cập qua Cloud9 IDE sử dụng phương thức SSH.

- **Create a new no-ingress EC2 instance for environment (access via Systems Manager)**: EC2 Instance cũng được khởi tạo cùng với Cloud9 environment và Instance có thể được truy cập qua System Manager.

- **Create and run in remote server (SSH connection)**: EC2 Instance đã có sẵn, Cloud9 eviroment thiết lập môi trường để đủ điều kiện truy cập EC2 Instance.

- Trong phạm vi bài giới thiệu về dịch vụ CloudFormation hôm nay, để đơn giản và tập trung vào nội dung chính chúng ta sẽ chọn option thứ nhất - EC2 và Cloud9 được khởi tạo cùng lúc và có thể truy cập Instance qua SSH.

- Các nội dung cấu hình còn lại ta để mặc định với:

- **Cost-saving setting**: sau 30’ nếu EC2 Instance không có tiến trình nào được chạy, Cloud9 sẽ stop Instance.

- **IAM Role**: AWSServiceRoleForAWSCloud9 - là service-linked role được tạo sẵn bởi AwS và gắn với dịch vụ Cloud9

- **Environment type**, chọn **Create a new EC2 instance for environment (direct access)**
- **Instance type**, chọn **t3.small(2GiB RAM + 2vCPU)**
- **Platform**, chọn **Amazon Linux 2 (recommended)**

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0004-createawscloud9.png)

5. Trong **Network settings**

- Chọn **AWS SSM**
- Chọn **Network (VPC)**
- Chọn **Public subnet**



![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0005-createawscloud9.png)

6. Chọn **Create**


![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0006-createawscloud9.png)

7. Giao diện môi trường vừa khởi tạo


![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0007-createawscloud9.png)

8. Trong giao diện môi trường vừa khởi tạo

- Chọn biểu tượng **R**
- Chọn **Manage EC2 Instance**


![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0008-createawscloud9.png)

9. Trong giao diện **EC2**

- Chọn **Action**
- Chọn **Security**
- Chọn **Modify IAM role**

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/0009-createawscloud9.png)

10. Trong giao diện **Modify IAM role**

- Chọn role đã tạo, bài lab này chọn **CloudFormation-Role**
- Chọn **Save**

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00010-createawscloud9.png)

11. Hoàn thành gán role thành công

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00011-createawscloud9.png)

12. Trong giao diện của môi trường **AWS Cloud9**

- Chọn **AWS Cloud9**
- Chọn **Preferences**

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00012-createawscloud9.png)

13.  Cloud9 sẽ quản lý thông tin chứng thực IAM một cách tự động. Chúng ta sẽ cần phải vô hiệu hóa tính năng này và sử dụng IAM Role.

- Chọn **AWS SETTINGS**
- Chọn **Credentials**
- Bỏ chọn **AWS managed temporary credentials**

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00013-createawscloud9.png)

14. Copy và Paste đoạn lệnh dưới đây vào Terminal của Cloud9 Workspace để cài đặt các công cụ hỗ trợ xử lý text trên dòng lệnh.

```
sudo yum -y install jq gettext bash-completion moreutils
```

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00014-createawscloud9.png)

15. Thực hiện cài đặt tool cfn-lint - là công cụ giúp bạn kiểm tra CloudFormation yaml/json templates và các thông tin khác. Bao gồm kiểm tra các thuộc tính của tài nguyên đã chính xác hay chưa hoặc thông tin cấu hình đã theo best practices hay chưa.

```
pip install cfn-lint
```

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00015-createawscloud9.png)

16. Kiểm tra cài đặt **cfn-lint** thành công bằng cách dùng lệnh sau:

```
cfn-lint --version
```

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00016-createawscloud9.png)

17. Cài đặt **taskcat**

```
pip install taskcat
```

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00017-createawscloud9.png)


18. Chúng ta sẽ cấu hình aws cli sử dụng Region hiện tại.

```
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
export AZS=($(aws ec2 describe-availability-zones --query 'AvailabilityZones[].ZoneName' --output text --region $AWS_REGION))
```

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00018-createawscloud9.png)

19. Chúng ta sẽ lưu các thông tin cấu hình vào bash_profile

```
echo "export ACCOUNT_ID=$ACCOUNT_ID" | tee -a ~/.bash_profile
echo "export AWS_REGION=$AWS_REGION" | tee -a ~/.bash_profile
echo "export AZS=${AZS[@]}" | tee -a ~/.bash_profile
aws configure set default.region $AWS_REGION
aws configure get default.region
```

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00019-createawscloud9.png)

20. Chúng ta sẽ sử dụng câu lệnh để kiểm tra Cloud9 IDE đang sử dụng IAM Role có chính xác không.

```
aws sts get-caller-identity --query Arn | grep CloudFormation-Role -q && echo "IAM role valid" || echo "IAM role NOT valid"
```

![CreateAWSCloud9](/images/3-CloudFormationbasic/3.1-CreateAWSCloud9/00020-createawscloud9.png)

