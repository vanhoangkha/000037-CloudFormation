---
title : "Create Stack"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 4.1.2 </b> "
---

#### Create Stack

1. Go to [AWS Management Console](https://aws.amazon.com/console/)

- Find **CloudFormaton**
- Select **CloudFormation**

![Create Stack](/images/4-CloudFormationadvance/4.1-Customresource/4.1.2-Createstack/0001-createstack.png)

2. In the **CloudFormation** interface

- Select **Stack**
- Select **Create stack**
- Select **With new resources (standard)**

![Create Stack](/images/4-CloudFormationadvance/4.1-Customresource/4.1.2-Createstack/0002-createstack.png)

3. In the **Create stack** interface

- Create a file **custom_resource_cfn_mr.yml**
- Then copy and paste the content of this code into the file and save it:

```
Parameters:
    SourceAccessCIDR:
      Type: String
      Description: The CIDR IP range that is permitted to access the instance. We recommend that you set this value to a trusted IP range.
      Default: 0.0.0.0/0
    SSHKeyName:
      Type: String
      Description: The name of the key that will be created
      Default: MyKey01
    AMIID:
      Type: String
      Description: The AMI ID that will be used to create EC2 instance
      Default: AMIID
    VPCPublicSubnet:
      Type: AWS::EC2::Subnet::Id
      Description: Choose a public subnet in the selected VPC
    VPC:
      Type: AWS::EC2::VPC::Id
      Description: The VPC in which to launch the EC2 instance. We recommend you choose your default VPC.
    FunctionArn:
      Type: String
      Description: The ARN of the lambda function that implements the custom resource
Resources:
  SSHKeyCR:
      Type: Custom::CreateSSHKey
      Version: "1.0"
      Properties:
        ServiceToken: !Ref FunctionArn
        KeyName: !Ref SSHKeyName
  MyEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      KeyName: !Ref SSHKeyName
      InstanceType: t2.micro
      ImageId: !Ref AMIID
      IamInstanceProfile: !Ref EC2InstanceProfile
      SecurityGroupIds:
        - !Ref EC2InstanceSG
      SubnetId: !Ref VPCPublicSubnet
      Tags:
        -
          Key: Name
          Value: Cfn-Workshop-Reinvent-2018-Lab1
    DependsOn: SSHKeyCR
  EC2InstanceProfile:
      Type: AWS::IAM::InstanceProfile
      Properties:
        Roles:
        - Ref: EC2InstanceRole
        Path: "/"
      DependsOn: EC2InstanceRole
  EC2InstanceRole:
    Type: AWS::IAM::Role
    Properties:
      Policies:
      - PolicyDocument:
          Version: '2012-10-17'
          Statement:
          - Action:
            - s3:GetObject
            Resource: "*"
            Effect: Allow
        PolicyName: s3-policy
      - PolicyDocument:
          Version: '2012-10-17'
          Statement:
          - Action:
            - logs:CreateLogStream
            - logs:GetLogEvents
            - logs:PutLogEvents
            - logs:DescribeLogGroups
            - logs:DescribeLogStreams
            - logs:PutRetentionPolicy
            - logs:PutMetricFilter
            - logs:CreateLogGroup
            Resource:
            - arn:aws:logs:*:*:*
            - arn:aws:s3:::*
            Effect: Allow
        PolicyName: cloudwatch-logs-policy
      - PolicyDocument:
          Version: '2012-10-17'
          Statement:
          - Action:
            - ec2:AssociateAddress
            - ec2:DescribeAddresses
            Resource:
            - "*"
            Effect: Allow
        PolicyName: eip-policy
      Path: "/"
      AssumeRolePolicyDocument:
        Statement:
        - Action:
          - sts:AssumeRole
          Principal:
            Service:
            - ec2.amazonaws.com
          Effect: Allow
        Version: '2012-10-17'
  EC2InstanceSG:
    Type: AWS::EC2::SecurityGroup
    Properties:
        GroupDescription: This should allow you to SSH to the instance from your location
        VpcId: !Ref VPC
        Tags:
        - Key: Name
          Value: !Sub ${AWS::StackName}
        SecurityGroupIngress:
          -
            Description: This should allow you to SSH from your location into an EC2 instance
            CidrIp: !Ref SourceAccessCIDR
            FromPort: 22
            IpProtocol: tcp
            ToPort: 22
Outputs:
  MyEC2InstanceDNSName:
    Description: The DNSName of the new EC2 instance
    Value: !GetAtt MyEC2Instance.PublicDnsName
  MyEC2InstancePublicIP:
    Description: The Public IP address of the newly created EC2 instance 
    Value: !GetAtt MyEC2Instance.PublicIp
```

- Select **Template is ready**
- Select **Upload a template file**
- Select **Choose file**
- Select **custom_resource_cfn_mr.yml**
- Select **Next**

![Create Stack](/images/4-CloudFormationadvance/4.1-Customresource/4.1.2-Createstack/0003-createstack.png)

4. In the **Stack name** section, enter ```ssh-key-gen-cr```

![Create Stack](/images/4-CloudFormationadvance/4.1-Customresource/4.1.2-Createstack/0004-createstack.png)

5. Access the [AWS Management Console] interface (https://aws.amazon.com/console/)

- Find **EC2**
- Select **EC2**

![Create Stack](/images/4-CloudFormationadvance/4.1-Customresource/4.1.2-Createstack/0005-createstack.png)

6. In the **EC2** interface

- Select **Instances**
- Select **Launch instances**

![Create Stack](/images/4-CloudFormationadvance/4.1-Customresource/4.1.2-Createstack/0006-createstack.png)

7. In the **Launch instances** interface

- Select **Quick Start**
- Copy **AMI ID**

![Create Stack](/images/4-CloudFormationadvance/4.1-Customresource/4.1.2-Createstack/0007-createstack.png)

8. In the **Create satck** interface

- **AMI ID**, enter the copied AMIID
- **FunctionArn**, enter the Arn of the created Lambda function
- **SSHKeyName**, enter the key pair name to create
- **SourceAccessCIDR**, enter ```0.0.0.0/0```
- Select **VPC**
- Select **Public Subnet**

![Create Stack](/images/4-CloudFormationadvance/4.1-Customresource/4.1.2-Createstack/0008-createstack.png)

9. Select **Next**

![Create Stack](/images/4-CloudFormationadvance/4.1-Customresource/4.1.2-Createstack/0009-createstack.png)

10. In the **Create stack** interface

- Select **I acknowledge that AWS CloudFormation might create IAM resources**
- Select **Create stack**

![Create Stack](/images/4-CloudFormationadvance/4.1-Customresource/4.1.2-Createstack/00010-createstack.png)

11. In the **CloudFormation** interface

- Select **Stack**
- Select **ssh-key-gen-cr** stack
- Select **Event** to view initialization events. Initialization to **CREATE_COMPLETE** is successful

![Create Stack](/images/4-CloudFormationadvance/4.1-Customresource/4.1.2-Createstack/00011-createstack.png)

12. In the **CloudFormation** interface

- Select **Resources**
- View initialized resources

![Create Stack](/images/4-CloudFormationadvance/4.1-Customresource/4.1.2-Createstack/00012-createstack.png)

13. In the **CloudFormation** interface

- Select **Parameters**
- View information **Key-Value**

![Create Stack](/images/4-CloudFormationadvance/4.1-Customresource/4.1.2-Createstack/00013-createstack.png)

14. In the **CloudFormation** interface

- Select **Outputs**
- View instance has been initialized

![Create Stack](/images/4-CloudFormationadvance/4.1-Customresource/4.1.2-Createstack/00014-createstack.png)

15. In the [AWS Management Console](https://aws.amazon.com/console/)

- Find **EC2**
- Select **EC2**

![Create Stack](/images/4-CloudFormationadvance/4.1-Customresource/4.1.2-Createstack/00015-createstack.png)

16. In the **EC2** interface

- Select **Instances**
- Select the newly created instance
- Select **Details**
- Select **public IPv4 address**
- View **IAM Role**

![Create Stack](/images/4-CloudFormationadvance/4.1-Customresource/4.1.2-Createstack/00016-createstack.png)