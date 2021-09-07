+++
title = "Tạo môi trường AWS Cloud9"
date = 2020
weight = 1
chapter = false
pre = "<b>1.1 </b>"
+++

#### Tạo môi trường AWS Cloud9

1 - Lựa chọn Region có tên **EU (Ireland)** với ID là **eu-west-1**.  
Workshop lần này yêu cầu chúng ta phải chọn chính xác Region bởi không phải tất cả các regions đều có số lượng dịch vụ giống nhau, một dịch vụ có thể chạy ở region này nhưng chưa chắc nó đã chạy ở region khác. Cloud9 là một dịch vụ như vậy.
![Security Hub](../../../images/1.png?width=15pc)
2 - Truy cập trang chủ dịch vụ [AWS Cloud9](https://ap-northeast-1.console.aws.amazon.com/cloud9/home/product). Bấm **Create Environment**. 
![Security Hub](../../../images/2.png?width=60pc)
3 - Nhập tên Environment Name ***ASG-Cloud9-Workshop*** rồi bấm **Next Step**
![Security Hub](../../../images/3.png?width=60pc) 
4 - Phần **Environment type** ở đây chúng ta có 3 lựa chọn 
 - **Create a new EC2 instance for environment (direct access)**: EC2 Instance được khởi tạo cùng với Cloud9 environment. Instance được truy cập qua Cloud9 IDE sử dụng phương thức SSH. 
 - **Create a new no-ingress EC2 instance for environment (access via Systems Manager)**: EC2 Instance cũng được khởi tạo cùng với Cloud9 environment và Instance có thể được truy cập qua System Manager.
 - **Create and run in remote server (SSH connection)**: EC2 Instance đã có sẵn, Cloud9 eviroment thiết lập môi trường để đủ điều kiện truy cập EC2 Instance.  

Trong phạm vi bài giới thiệu về dịch vụ CloudFormation hôm nay, để đơn giản và tập trung vào nội dung chính chúng ta sẽ chọn option thứ nhất - EC2 và Cloud9 được khởi tạo cùng lúc và có thể truy cập Instance qua SSH.  
Các nội dung cấu hình còn lại ta để mặc định với: 
 - **Cost-saving setting**: sau 30' nếu EC2 Instance không có tiến trình nào được chạy, Cloud9 sẽ stop Instance. 
 - **IAM Role**: ***AWSServiceRoleForAWSCloud9*** - là service-linked role được tạo sẵn bởi AwS và gắn với dịch vụ Cloud9. 
![Security Hub](../../../images/4.png?width=60pc)

5 - Thực hiện review lần cuối rồi bấm **Create Environment**
![Security Hub](../../../images/5.png?width=60pc)
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

