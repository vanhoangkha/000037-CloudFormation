---
title : "Các tài nguyên tùy chỉnh"
date: 2024-01-01
weight : 1 
chapter : false
pre : " <b> 4.1 </b> "
---

#### Các tài nguyên tùy chỉnh

- Bạn có thể mở rộng khả năng sử dụng của CloudFormation với các tài nguyên tùy chỉnh bằng cách ủy quyền công việc cần thực hiện cho một hàm Lambda đã được thiết kế đặc biệt, để tương tác với dịch vụ CloudFormation.

- Trong nội dung của mã code, bạn sẽ cài đặt các hàm tạo, cập nhật và xóa, và hàm gửi phản hồi về trạng thái của hoạt động đó.

- Trong bài lab đầu tiên này, bạn sẽ tạo một tài nguyên tùy chỉnh mà có thể sinh khóa SSH và lưu trữ nó trong kho tham số SSM.

#### Nội dung

1. [Tạo Lambda Function](4.1.1-createlambdafunction)
2. [Tạo Stack](4.1.2-createstack)
3. [Kết nối EC2 Instance](4.1.3-accessinstance)

