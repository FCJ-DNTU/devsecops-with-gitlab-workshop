+++
title = "Nạp dữ liệu cho SQL Server"
date = "`r Sys.Date()`" 
weight = 4
chapter = false
pre = "<b>3.4 </b>"
+++

Ở mục trước chúng ta đã triển khai thành công Frontend và Backend. Bây giờ chúng ta tiến hành thiết lập cho SQL Server

### 1. Thiết lập SQL Server

Chúng ta sẽ sử dụng câu lệnh sau để kết nối tới giao diện database sql trong docker

```bash
docker exec -it sqlserver /opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P Str0ngPa5sVvorcl -C
```

![image.png](/images/3-implementproject/3.4-sqlserver/image.png?featherlight=false&width=60pc)

Chúng ta sẽ gõ câu lệnh như sau:

```bash
create database ecommerce;
GO
```

![image.png](/images/3-implementproject/3.4-sqlserver/image1.png?featherlight=false&width=60pc)


### 2. Thiết lập Cloud Beaver

Ở các bài trước thì giao diện cảu Cloud Beaver sẽ như thế này và chúng ta bấm Next:

bấm Next.

![image.png](/images/3-implementproject/3.4-sqlserver/image2.png?featherlight=false&width=60pc)


Ở giao diện kế tiếp chúng ta kéo xuống, đặt tài khoản và mật khẩu để đăng nhập

![image.png](/images/3-implementproject/3.4-sqlserver/image3.png?featherlight=false&width=60pc)


Nhấn Finish

![image.png](/images/3-implementproject/3.4-sqlserver/image4.png?featherlight=false&width=60pc)


Tiến hành đăng nhập lại

![image.png](/images/3-implementproject/3.4-sqlserver/image5.png?featherlight=false&width=60pc)


Bấm vào biểu tượng logo

![image.png](/images/3-implementproject/3.4-sqlserver/image6.png?featherlight=false&width=60pc)


Bấm vào biểu tượng dấu cộng và nhấn New Connection

![image.png](/images/3-implementproject/3.4-sqlserver/image7.png?featherlight=false&width=60pc)


Chúng ta đang thực hành với SQL Server nên chọn bấm chọn

![image.png](/images/3-implementproject/3.4-sqlserver/image8.png?featherlight=false&width=60pc)


Chungs ta sẽ điền thông tin 

- Host: địa chi ipv4 của bạn
- Database: ecommerce
- User name: sa
- User password: Str0ngPa5sVvorcl

![image.png](/images/3-implementproject/3.4-sqlserver/image9.png?featherlight=false&width=60pc)


Sau khi điền xong chúng ta sẽ bấm Test để kiểm tra kết nối

![image.png](/images/3-implementproject/3.4-sqlserver/image10.png?featherlight=false&width=60pc)


Kết quả đã kết nối thành công! Và chúng ta bấm Create để tạo kết nối

![image.png](/images/3-implementproject/3.4-sqlserver/image11.png?featherlight=false&width=60pc)


Chúng ta sẽ có giao diện như sau. Chúng ta sẽ bấm biểu tượng SQL

![image.png](/images/3-implementproject/3.4-sqlserver/image12.png?featherlight=false&width=60pc)


Chúng ta cat câu lệnh sql từ file table-init.sql

```bash
cat /root/ecommerce-fullstack-netcore-react/table-init.sql
```

![image.png](/images/3-implementproject/3.4-sqlserver/image13.png?featherlight=false&width=60pc)


Copy chúng vào sql trên Cloud Beaver và tiến hành chạy toàn bộ câu lệnh sql

![image.png](/images/3-implementproject/3.4-sqlserver/image14.png?featherlight=false&width=60pc)

Kết quả tạo bảng chạy thành công 

![image.png](/images/3-implementproject/3.4-sqlserver/image15.png?featherlight=false&width=60pc)

Chúng ta tiếp tục cat dữ liệu từ file data-init.sql

```bash
cat /root/ecommerce-fullstack-netcore-react/data-init.sql
```

![image.png](/images/3-implementproject/3.4-sqlserver/image16.png?featherlight=false&width=60pc)


Chúng ta sẽ copy sang và chạy toàn bộ file và kết quả thành công

![image.png](/images/3-implementproject/3.4-sqlserver/image17.png?featherlight=false&width=60pc)


Bây giờ dữ liệu đã được nạp cho database. Chúng ta sẽ kiểm tra lại Frontend bằng cách reload lại. Kết quả là đã có sản phẩm

![image.png](/images/3-implementproject/3.4-sqlserver/image18.png?featherlight=false&width=60pc)


 Và backend trả về dữ liệu của các sản phẩm.

![image.png](/images/3-implementproject/3.4-sqlserver/image19.png?featherlight=false&width=60pc)


Vậy là chúng ta đã triển khai dự án fullstack bằng thủ công!