+++
title = "Giới thiệu về dự án Fullstack"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>3.1 </b>"
+++

### 1. Công nghệ sử dụng:

- ReactJS - Node 18
- .NetCore 6
- SQL Server

### 2. Mô hình dự án

![image.png](/images/3-implementproject/3.1-intro/image.png?featherlight=false&width=60pc)

Luồng hoạt động trong mô hình như sau:

1. **Frontend (Giao diện người dùng)**:
    - Sử dụng **React** làm framework để xây dựng giao diện web. React sẽ tạo ra các thành phần giao diện (components) cho người dùng tương tác.
    - Khi người dùng tương tác với giao diện, React sẽ gửi các yêu cầu (requests) đến backend thông qua RESTful API.
2. **Backend (Dịch vụ phía máy chủ)**:
    - Sử dụng **.NET 6**, là một framework của Microsoft, để xây dựng các dịch vụ backend.
    - Backend nhận các yêu cầu từ frontend, xử lý logic nghiệp vụ và thực hiện các thao tác cần thiết (chẳng hạn như truy xuất dữ liệu từ cơ sở dữ liệu hoặc thực hiện tính toán).
    - Sau khi xử lý xong, backend trả kết quả về cho frontend thông qua RESTful API.
3. **Database (Cơ sở dữ liệu)**:
    - Backend tương tác với **SQL Server**, là một hệ quản trị cơ sở dữ liệu quan hệ của Microsoft, để lưu trữ và truy xuất dữ liệu.
    - Dữ liệu từ cơ sở dữ liệu được truy xuất bởi backend và gửi lại cho frontend nếu cần.