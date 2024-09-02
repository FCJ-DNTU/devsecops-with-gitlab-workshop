+++
title = "Introduction to GitLab"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>1.1 </b>"
+++

# Tạo VPC

1. Truy cập [AWS Management Console](https://aws.amazon.com/vi/console/)
    - Tìm **VPC**
    - Chọn **Create VPC**

![image.png](2%201%20Ta%CC%A3o%20VPC%20va%CC%80%20EC2%209f66f339716f407fbece0f9e6e3243f9/image.png)

1. Trong giao diện **VPC settings**
    - **Resources to create**, chọn **VPC and more**
    - **Name tag auto-generation**, nhập **Devsecops VPC**
    - **IPv4 CIDR block**, nhập **10.0.0.0/16**

![image.png](2%201%20Ta%CC%A3o%20VPC%20va%CC%80%20EC2%209f66f339716f407fbece0f9e6e3243f9/image%201.png)

1. Chọn các AZs theo hình và bấm chọn **Create VPC**

![image.png](2%201%20Ta%CC%A3o%20VPC%20va%CC%80%20EC2%209f66f339716f407fbece0f9e6e3243f9/image%202.png)

1. Sau khi tạo xong, chúng ta sẽ có một VPC như thế này

![image.png](2%201%20Ta%CC%A3o%20VPC%20va%CC%80%20EC2%209f66f339716f407fbece0f9e6e3243f9/image%203.png)

# Tạo EC2 Instance

1. Truy cập [AWS Management Console](https://aws.amazon.com/vi/console/)
    - Tìm **EC2**
        
        ![image.png](2%201%20Ta%CC%A3o%20VPC%20va%CC%80%20EC2%209f66f339716f407fbece0f9e6e3243f9/image%204.png)
        
    - Chọn **Launch Instance**
        
        ![image.png](2%201%20Ta%CC%A3o%20VPC%20va%CC%80%20EC2%209f66f339716f407fbece0f9e6e3243f9/image%205.png)
        
2. Trong giao diện **Launch an instance**
    - **Name and tags**, chọn

![image.png](2%201%20Ta%CC%A3o%20VPC%20va%CC%80%20EC2%209f66f339716f407fbece0f9e6e3243f9/image%206.png)

1. Ở mục **Application and OS Images (Amazon Machine Image)**
    - Chọn **Ubuntu Server 22.04 LTS (HVM), SSD Volume Type**

![image.png](2%201%20Ta%CC%A3o%20VPC%20va%CC%80%20EC2%209f66f339716f407fbece0f9e6e3243f9/image%207.png)

1. Ở mục **Instance type** và **Key pair**
    - Chọn **t3.medium** bởi vì chúng ta sẽ triển khai chung database server trên Deploy Instance này nên cần nhều bộ nhớ.
    - Tạo ra một **key pair** đặt tên là  **devsecops-kp**

![image.png](2%201%20Ta%CC%A3o%20VPC%20va%CC%80%20EC2%209f66f339716f407fbece0f9e6e3243f9/image%208.png)

1. Ở mục **Network settings**
    - Chọn VPC mà bạn mới tạo
    - Chọn **public-subnet-1**
    - Bật **Auto-assign public IP**
    - Tạo một **Security Group**

![image.png](2%201%20Ta%CC%A3o%20VPC%20va%CC%80%20EC2%209f66f339716f407fbece0f9e6e3243f9/image%209.png)

1. Ở mục **Configure storage** thay đổi bộ nhớ thành **30GB** va nhấn **Launch Instance**

![image.png](2%201%20Ta%CC%A3o%20VPC%20va%CC%80%20EC2%209f66f339716f407fbece0f9e6e3243f9/image%2010.png)

1. Vậy là chúng ta đã hoàn thành việc khởi tạo EC2 Instance.

![image.png](2%201%20Ta%CC%A3o%20VPC%20va%CC%80%20EC2%209f66f339716f407fbece0f9e6e3243f9/image%2011.png)