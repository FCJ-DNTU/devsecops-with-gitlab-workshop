+++
title = "Kết nối với EC2 Instance từ VSCode"
date = "`r Sys.Date()`" 
weight = 3
chapter = false
pre = "<b>2.3 </b>"
+++

Kết nối SSH từ Visual Studio Code đến EC2 Instance là giải pháp nhanh chóng thay thế cho việc sử dụng Cloud9.

##### 1. Chúng ta sẽ tải Visual Studio Code và extensions có tên là **Remote - SSH**
    
Bạn có thể tải vs code tại đây: [Download VSCode](https://code.visualstudio.com/download)

Sau khi tải xong. Chúng ta sẽ tải extension **Remote -SSH**
    

![image.png](/images/2-preparation/2.3-vscodessh/000-connectec2.png?featherlight=false&width=60pc)


##### 2. Sau khi tải xong chúng ta nhấn có biểu tượng ở phía dưới góc trái màn hình một hộp thoại sẽ được mở ra.

![image.png](/images/2-preparation/2.3-vscodessh/001-connectec2.png?featherlight=false&width=60pc)

##### 3. Chúng ta sẽ nhấn vào Connect to Host.

![image.png](/images/2-preparation/2.3-vscodessh/002-connectec2.png?featherlight=false&width=60pc)

##### 4. Nhấn vào **Add New SSH Host**.

![image.png](/images/2-preparation/2.3-vscodessh/003-connectec2.png?featherlight=false&width=60pc)

##### 5. Trong ô input nhập **deploy-instance-remote** và nhấn Enter.

![image.png](/images/2-preparation/2.3-vscodessh/image.png?featherlight=false&width=60pc)

##### 6. Nhấn vào đường dẫn trong **C:\Users\ADMIN.ssh\config** để cấu hình

![image.png](/images/2-preparation/2.3-vscodessh/005-connectec2.png?featherlight=false&width=60pc)

![image.png](/images/2-preparation/2.3-vscodessh/image%201.png?featherlight=false&width=60pc)

##### 7. Ngay tại khối code SSH name mới được cấu hình trước đó hãy thay đổi thông tin đúng với địa chỉ **IPv4 của EC2 Instance** và đường dẫn đến **Key Pair** trong máy của bạn.

```bash
Host deploy-instance-remote
  HostName 3.106.228.72
  User ubuntu
  IdentityFile "C:\Users\ADMIN\Downloads\devsecops-kp.pem"
```
![image.png](/images/2-preparation/2.3-vscodessh/image%202.png?featherlight=false&width=60pc)

##### 8. Bấm vào biểu tượng SSH ở dưới cùng góc bên trái và bắt đầu thực hiện Connect.

![image.png](/images/2-preparation/2.3-vscodessh/image%203.png?featherlight=false&width=60pc)

##### 9. Khi bấm vào một cửa sổ mới sẽ hiển thị và nhấp chọn Chọn **Continue**

![image.png](/images/2-preparation/2.3-vscodessh/image%204.png?featherlight=false&width=60pc)

##### 10. Nhấn vào **Linux** đợi một chút để chương trình nạp VSCode Server vào trong EC2 Instance 

![image.png](/images/2-preparation/2.3-vscodessh/008-connectec2.png?featherlight=false&width=60pc)

##### 11. Chọn **Open Folder** và Click **OK**
![image.png](/images/2-preparation/2.3-vscodessh/image%205.png?featherlight=false&width=60pc)

##### 12. Đây là giao diện sau khi kết nối
![image.png](/images/2-preparation/2.3-vscodessh/image%206.png?featherlight=false&width=60pc)