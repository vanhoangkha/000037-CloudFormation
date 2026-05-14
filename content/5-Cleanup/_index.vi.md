---
title : "Dọn dẹp tài nguyên"
date: 2024-01-01
weight : 5
chapter : false
pre : " <b> 5. </b> "
---

#### Delete Cloud9 Environment

1. Chọn **Region** chứa môi trường cần xóa
2. Trong giao diện **Cloud9** chọn môi trường cần xóa
3. Chọn **Delete**
![Clean](/images/5-CleanUpResources/1.png)
4. Xác thực xóa môi trường bằng cách điền **Delete** và chọn **Delete**
![Clean](/images/5-CleanUpResources/2.png)

#### Delete a stack on the AWS CloudFormation console

1. Truy cập vào trang [AWS CloudFormation](https://console.aws.amazon.com/cloudformation/)
2. Trong giao diện **CloudFormation** chọn **Stack**
3. Chọn **Stack** cần xóa
4. Chọn **Delete**
5. Xác minh xóa stack
6. Đợi vài phút stack chuyển trạng thái sang **DELETE_COMPLETE** là xóa thành công
![Clean](/images/5-CleanUpResources/3.png)


#### Delete a stack set

1. Truy cập vào trang [AWS CloudFormation](https://console.aws.amazon.com/cloudformation/)
2. Chọn **StackSets**
3. Chọn **Stackset** cần xóa
4. Chọn **Actions** sau đó chọn **Delete StackSet**
5. Xác minh xóa stack set và chọn **Delete StackSet**
