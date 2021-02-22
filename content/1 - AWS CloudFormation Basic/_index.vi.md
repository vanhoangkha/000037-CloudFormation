+++
title = "Thiết lập Tài Khoản AWS"
date = 2020
weight = 1
chapter = false
pre = "<b>1. </b>"
+++

---

Máy ảo EC2 là một máy ảo chạy trên EBS (nghĩa là ổ đĩa chính của máy ảo sử dụng EBS). Bạn có thể lựa chọn bất kì một Availability Zone cho máy ảo hoặc để dịch vụ Amazon EC2 chọn Availability Zone tự động. Khi khởi tạo một máy ảo, chúng ta sẽ bảo mật máy ảo này thông qua việc lựa chọn Keypair và Security Group. Khi kết nối vào máy ảo này, bạn sẽ phải sử dụng Private key của Keypair đã gắn với máy ảo.

![Tổng quan Mô hình triển khai](/images/3-ec2/1/overview_ec2.png)