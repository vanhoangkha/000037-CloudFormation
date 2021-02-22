+++
title = "Tạo Volume Snapshot"
date = 2020-04-18T00:38:32+07:00
weight = 3
chapter = true
pre = "<b>3.3. </b>"
+++

## 3.3. Tạo một Snapshot EBS Volume của EC2 Instance

### Tổng quan
---
Chúng ta có thể tạo một snapshot của một EBS volume tại một thời điểm và sử dụng nó như một bản khởi đầu cho các volume mới hoặc phục vụ việc sao lưu dữ liệu. Nếu chúng ta tạo snapshot của một volume định kỳ , các snapshot đó sẽ là các **incremental snapshot**, nghĩa là **chỉ lưu những phần đã thay đổi** so với snapshot cuối cùng của volume đó.

Phương thức tạo snapshot là **bất đồng bộ**; thời điểm tạo snapshot lưu lại ngay lập tức nhưng trạng thái của snapshot sẽ là ```pending``` cho đến khi việc tạo snapshot hoàn tất (khi tất cả thay đổi được lưu hoàn toàn vào Amazon S3). Việc này thường mất nhiều giờ để thực hiện đối với các snapshot khởi đầu có dung lượng lớn hoặc các snapshot có nhiều dữ liệu thay đổi. Khi đang ở trạng thái đang hoàn thành, snapshot sẽ không bị ảnh hưởng bởi các tác vụ đọc và ghi vào volume.

Chúng ta có thể tạo snapshot cho một volume đang được sử dụng. Tuy nhiên, snapshot chỉ ghi lại dữ liệu đã được lưu vào volume EBS của chúng ta tại thời điểm lệnh tạo snapshot được truyền vào. Vì vậy, bất kỳ dữ liệu nào được lưu dạng cache bởi bất kỳ chương trình hay hệ điều hành sẽ **không được lưu lại**.

Nếu có thể tạm dừng việc ghi dữ liệu vào ổ đĩa đủ lâu để tạo snapshot thì snapshot tạo ra sẽ hoàn hảo. Tuy nhiên, nếu chúng ta không thể tạm dừng việc ghi tất cả dữ liệu vào volume, chúng ta nên unmount volume trong máy ảo, thực hiện tạo snapshot, sau đó remount volume trở lại để đảm bảo tính nhất quán và toàn vẹn của snapshot. Chúng ta vẫn có thể remount và sử dụng volume khi snapshot đang ở trạng thái ```pending```.

Để việc quản lý snapshot dễ dàng, chúng ta có thể gắn **Tag** vào snapshot của mình trong quá trình tạo hoặc sau khi tạo. Ví dụ, chúng ta có thể gắn những tag để miêu tả chi tiết trạng thái của volume lúc chúng ta thực hiện snapshot hoặc miêu tả về tên thiết bị để gắn volume lúc tạo snapshot.

### Mã hóa Snapshot 
---
Snapshot được tạo ra từ những **volume được mã hóa** thì sẽ **tự động được mã hóa**. Những volume được tạo thành **từ các snapshot có mã hóa** cũng sẽ **được mã hóa tự động**. Dữ liệu lưu trong những volume mã hóa và các snapshot liên quan đều được bảo vệ trong cả lúc được truyền đi và lúc không hoạt động.  

Mặc định, chúng ta chỉ có thể tạo volume từ các snapshot mà chúng ta sở hữu. Tuy nhiên, vẫn có thể chia sẻ những snapshot **không bị mã hóa** với một số tài khoản AWS nhất định hoặc toàn bộ cộng động AWS bằng cách để trạng thái công khai cho snapshot.  

Chúng ta chỉ có thể chia sẻ những snapshot được mã hóa với **một số tài khoản AWS nhất định**. Để người khác có thể sử dụng những snapshot này, chúng ta **phải chia sẻ CMK key được dùng để mã hóa các snapshot**. Các người dùng khi sử dụng snapshot này **buộc phải tạo một bản sao** của snapshot và sử dụng bản sao này để khôi phục volume ban đầu. Các bản sao trên **có thể được mã hóa lại bằng một key khác**.

> **Ghi chú**: 
> Nếu chúng ta tạo một bản sao và mã hóa bản sao này với một CMK key mới, một bản sao hoàn toàn mới (non-incremental) sẽ luôn được tạo ra và điều này sẽ dẫn đến việc tạo ra thêm thời gian và chi phí lưu trữ.

### Tạo một snapshot
---
Sử dụng các bước bên dưới để tạo ra một snapshot từ một volume bất kỳ.

#### Tạo snapshot bằng Bảng điều khiển

---

1. Mở Bảng điều khiển Amazon EC2 tại địa chỉ https://console.aws.amazon.com/ec2/
2. Chọn **Snapshots** trong mục **Elastic Block Store** trên thanh điều hướng.

![Bảng điều khiển Snapshot](/images/3-ec2/3/3-1-snapshot-dashboard.png?width=90pc)

3. Chọn **Create Snapshot**.
4. Mục **Volume**, chọn volume đang gắn vào máy ảo EC2.

![Tạo một Snapshot](/images/3-ec2/3/3-2-create-snapshot.png?width=90pc)

5. (Không bắt buộc) Điền mô tả cho snapshot.
6. (Không bắt buộc) Chọn **Add Tag** để gắn tag cho snapshot. Mỗi tag sẽ bao gồm 1 khóa và 1 giá trị.
7. Chọn **Create Snapshot**.

![Tạo Snapshot Thành Công](/images/3-ec2/3/3-3-snapshot-success.png?width=90pc)

#### Tạo snapshot bằng giao diện dòng lệnh

---

Chúng ta có thể sử dụng một trong những command sau:

- [create-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-snapshot.html) (AWS CLI)
- [New-EC2Snapshot](https://docs.aws.amazon.com/powershell/latest/reference/items/New-EC2Snapshot.html) (AWS Tools cho Windows PowerShell)

### Xóa một Snapshot
---
Khi chúng ta xóa một snapshot, chỉ những dữ liệu được liên kết liên quan trực tiếp với snapshot đó sẽ bị xóa. Dữ liệu của một snapshot cụ thể là duy nhất sẽ không bị xóa trừ khi tất cả các snapshot liên kết đến dữ liệu đó bị xóa. Xóa các snapshot trước đó của một volume **không ảnh hưởng** đến khả năng khôi phục volume của chúng ta từ các snapshot sau đó của volume.

Xóa một snapshot **không ảnh hưởng** gì đến volume liên quan và xóa một volume sẽ **không ảnh hưởng** đến snapshot được tạo ra từ volume đó.

Nếu chúng ta tạo snapshot một cách định kỳ, snapshot sẽ là incremental snapshot. Tuy vậy, quá trình xóa snapshot lại được thiết kế và **chỉ yêu cầu chúng ta giữ lại snapshot cuối cùng** của volume để khôi phục lại volume này. Dữ liệu trên một volume, **được lưu trữ trong một hoặc một loạt** các snapshot trước đó và sau này bị xóa thì những liệu này vẫn được coi là duy nhất đối với các snapshot trước đó. Dữ liệu duy nhất này không bị xóa khỏi loạt snapshot này trừ khi tất cả các snapshot liên kết đến dữ liệu duy nhất này bị xóa.

Xóa một snapshot **có thể không giúp giảm chi phí lưu trữ** của công ty chúng ta. Các snapshot khác có thể liên kết tới dữ liệu của các snapshot bị xóa và dữ liệu liên kết luôn được lưu trữ. Nếu chúng ta xóa một snapshot mang dữ liệu được dùng bởi các snapshot khác sau này, chi phí lưu trữ dữ liệu này sẽ được chuyển sang các snapshot sau đó.

Trạng thái của volume 1 được biểu diễn tại 3 thời điểm khác nhau trong sơ đồ bên dưới. Trong trạng thái đầu và thứ hai, một snapshot được tạo ra và trạng thái cuối, một snapshot bị xóa đi.

![Tổng quan quá trình xóa snapshot](/images/3-ec2/3/3-4-deleting-snapshot-overview.png?width=40pc)

- Ở **trạng thái 1**, volume chứa 10GB dữ liệu. Do **Snapshot A** là snapshot đầu tiên được tạo ra của volume, toàn bộ 10GB dữ liệu của volume đều được sao chép.
- Ở **trạng thái 2**, volume vẫn chứa 10GB dữ liệu nhưng đã có 4GB dữ liệu bị thay đổi. **Snapshot B** lúc này chỉ cần sao chép và lưu trữ **4GB dữ liệu bị thay đổi** so với thời điểm **Snapshot A** được tạo ra. Toàn bộ **6GB dữ liệu còn lại không thay đổi** đã được lưu trữ ở **Snapshot A**, **Snapshot B** chỉ cần liên kết tới những dữ liệu này thay vì sao chép lại một lần nữa. Dấu mũi tên đứt nét miêu tả quá trình này.
- Ở **trạng thái 3**, volume không thay đổi gì so với **trạng thái 2** nhưng **Snapshot A** đã bị xóa. Lúc này, toàn bộ 6GB dữ liệu mà **Snapshot B** liên kết tới ở **Snapshot A** sẽ được **chuyển sang Snapshot B** và được biểu diễn bởi dấu mũi tên đậm và kết quả là chúng ta **vẫn bị tính phí cho việc lưu trữ 10GB dữ liệu** bao gồm 6GB dữ liệu không thay đổi được giữ lại khi xóa **Snapshot A** và 4 GB dữ liệu thay đổi được lữu trữ ở **Snapshot B**.

> **Lưu ý**: 
> Chúng ta không thể xóa snapshot của thiết bị gốc của một volume EBS được sử dụng bởi các AMI đã thiết lập. Trước tiên, chúng ta phải hủy thiết lập của AMI trước khi chúng ta có thể xóa snapshot này.

#### Xóa một Snapshot bằng Bảng điều khiển

---

1. Mở Bảng điều khiển EC2 tại https://console.aws.amazon.com/ec2/.
2. Chọn **Snapshots** ở thanh điều hướng.
3. Chọn snapshot và chọn **Delete** từ menu **Actions**.
4. Chọn **Yes, Delete** để xóa.

![Xóa Snapshot](/images/3-ec2/3/3-5-deleting-snapshot.png?width=90pc)

#### Xóa một Snapshot bằng Giao diện dòng lệnh

---

Chúng ta có thể sử dụng một trong những command sau:

- [delete-snapshot](https://docs.aws.amazon.com/cli/latest/reference/ec2/delete-snapshot.html) (AWS CLI)
- [Remove-EC2Snapshot](https://docs.aws.amazon.com/powershell/latest/reference/items/Remove-EC2Snapshot.html) (AWS Tools cho Windows PowerShell)

> **Ghi chú**: 
> Mặc dù chúng ta có thể xóa một snapshot đang trong quá trình khởi tạo, nhưng snapshot sẽ được tạo hoàn thành trước khi bị xóa. Việc này sẽ mất một khoản thời gian. Nếu chúng ta đang ở ngưỡng giới hạn tạo snapshot (năm (5) snapshot đang được tạo) thì khi tạo thêm snapshot mới chúng ta sẽ nhận được lỗi ```ConcurrentSnapshotLimitExceeded```.
