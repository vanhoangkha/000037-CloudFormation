+++
title = "Truy cập khi mất Keypair"
date = 2020-04-18T00:38:32+07:00
weight = 2
chapter = true
pre = "<b>3.2. </b>"
+++

## 3.2. Truy cập vào máy ảo EC2 Linux khi mất Key pair
---
To change the key pair, create an AMI of the existing instance, and then launch a new instance. You can then select a new key pair by following the instance launch wizard. Follow these steps:

> **Cảnh báo**:   
> Trước khi thực hiện, cần lưu ý các bước sau:
>  
> - Stop và Restart máy ảo sẽ xóa các dữ liệu trên **Instance Store Volumes**. Cần chắc chắn đã sao lưu các dữ liệu lưu trữ trên Instance Store Volume trước khi thực hiện.
> - Stop và Restart máy ảo sẽ thay đổi địa chỉ **IP Public**. Elastic IP được khuyến cáo sử dụng thay cho địa chỉ IP public khi sử dụng cho truy cập dịch vụ từ bên ngoài.

1. Truy cập đến **EC2 Management Console**, chọn **Key Pairs** ở mục **Network & Security** trong thanh điều hướng bên trái.

![Bảng điều khiển Key pair](/images/3-ec2/2/2-1-keypair-dashboard.png?width=90pc)

2. Tạo một **key pair mới**.

![Tạo một key pair mới](/images/3-ec2/2/2-2-create-new-keypair.png?width=90pc)

3. Nếu khởi tạo key pair thông qua Amazon EC2 console, chúng ta cần tải key pair về máy để lấy thông tin public key.

![Tạo key pair thành công](/images/3-ec2/2/2-3-success-create-keypair.png?width=90pc)

4. Để lấy được public key, ta có thể sử dụng PuTTYGen để mở tập tin keypair và lưu/sao chép thông tin public key để sử dụng ở các bước tiếp theo.

![Lấy thông tin Public Key](/images/3-ec2/2/2-4-get-public-key.png?width=90pc)

5. Truy cập đến **Amazon EC2 console**.
6. Stop máy ảo cần truy cập.

![Stop Máy ảo](/images/3-ec2/2/2-6-stopped-instance.png?width=90pc)

7. Chọn **Actions > Instance Settings > View/Change User Data**.

![Thay đổi thông tin User Data](/images/3-ec2/2/2-7-select-instance-to-change-user-data.png?width=90pc)

8. Sao chép đoạn script sau vào hộp thoại **View/Change User Data**:

```Content-Type: multipart/mixed; boundary="//"
MIME-Version: 1.0

--//
Content-Type: text/cloud-config; charset="us-ascii"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
Content-Disposition: attachment; filename="cloud-config.txt"

#cloud-config
cloud_final_modules:
- [users-groups, once]
users:
  - name: ```username```
    ssh-authorized-keys: 
    - ```PublicKeypair```
```

- Thay thế ```username``` với username của máy ảo, ví dụ như ```ec2-user```. Chúng ta có thể sử dụng username mặc định hoặc username tự tạo (nếu đã khởi tạo ở trong máy ảo).
- Thay thế ```PublicKeypair``` với nội dung Public key lấy được ở **Bước 4**. Chắc chắn rằng toàn bộ public key được nhập vào, bắt đầu với ```ssh-rsa```.  
Ví dụ trong bài thực hành này:  
```ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCk1OSmXY52TPJAgjuL8gv8ekQBqKqR6JxGJTBsdS4hKwA5GvRB8yAqftiOPObylCvAf5sZSA2ual2RgZksDDNUBmcnO2Eg9D1G2he01UhG39cEjTns2X/R/No3yIaeytYXh+qu0QciR9sJy6jvx6Mbn3BgWn44sjjw9tEE8zj9p9qYZ2MSVK5QRYOjkPzjy9eOwo3UtMODIj9+uaGw6imLjfgdMOB3OkSqPlMW0fCl2xYcq5BrPKXJTGjU3k5kXe/7zKA+Tiy4IS7BUgVYWg0zSQncbiyzgO8tgsTZI8IDUAmycYCxNlw1X6W7BaUDWw/6AYkOc0w882UIm8OrCgyR```

9. Chọn **Save**.

![Lưu thông tin User Data](/images/3-ec2/2/2-8-change-instance-user-data.png?width=90pc)

10. Chạy máy ảo bằng việc chọn **Actions > Instance State > Start**.

![Chạy máy ảo](/images/3-ec2/2/2-9-start-instance.png?width=90pc)

11. Sau khi máy ảo khởi chạy qua giai đoạn ```cloud-init```, public key mới đã được thay đổi trên máy ảo.

> **Quan trọng**: 
> Bởi vì trong script có thông tin về Key pair nên ta cần xóa bở nội dung này khỏi **User Data** sau khi hoàn thành.

12. **Stop** máy ảo.
13. Chọn **Actions > Instance Settings > View/Change User Data**.
14. Xóa toàn bộ đoạn script đã thêm vào hộp thoại **View/Change User Data** ở trên và chọn **Save**.
15. **Start** máy ảo trở lại.
16. Bây giờ, chúng ta đã có thể kết nối lại máy ảo với Key pair mới.

![Connecting to Instance with new Keypair](/images/3-ec2/2/2-11-connected-to-instance.png?width=90pc)

> **Ghi chú**: 
> Nếu máy ảo Linux đang chạy Amazon Linux 2 2.0.20190618 hoặc mới hơn, chúng ta có thể sử dụng **EC2 Instance Connect** để kết nối đến máy ảo Linux.
