+++
title = "Tạo VPC và EC2 Instance"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>2.1 </b>"
+++

### 1. Tạo VPC

1.1 Truy cập [AWS Management Console](https://aws.amazon.com/vi/console/)
- Tìm **VPC**
- Chọn **Create VPC**

![image.png](/images/2-preparation/2.1-createvpcec2/image.png?featherlight=false&width=60pc)

1.2 Trong giao diện **VPC settings**
- **Resources to create**, chọn **VPC and more**
- **Name tag auto-generation**, nhập **Devsecops VPC**
- **IPv4 CIDR block**, nhập **10.0.0.0/16**

![image.png](/images/2-preparation/2.1-createvpcec2/image1.png?featherlight=false&width=30pc)

1.3 Chọn các AZs theo hình và bấm chọn **Create VPC**

![image.png](/images/2-preparation/2.1-createvpcec2/image2.png?featherlight=false&width=30pc)

1.4 Sau khi tạo xong, chúng ta sẽ có một VPC như thế này

![image.png](/images/2-preparation/2.1-createvpcec2/image3.png?featherlight=false&width=60pc)

### 2. Tạo EC2 Instance

2.1 Truy cập [AWS Management Console](https://aws.amazon.com/vi/console/)
- Tìm **EC2**
    
    ![image.png](/images/2-preparation/2.1-createvpcec2/image4.png?featherlight=false&width=60pc)
        
- Chọn **Launch Instance**
    
    ![image.png](/images/2-preparation/2.1-createvpcec2/image5.png?featherlight=false&width=60pc)
        
2.2 Trong giao diện **Launch an instance**
- **Name and tags**: Deploy Instance

![image.png](/images/2-preparation/2.1-createvpcec2/image6.png?featherlight=false&width=60pc)

2.3 Ở mục **Application and OS Images (Amazon Machine Image)**
- Chọn **Ubuntu Server 22.04 LTS (HVM), SSD Volume Type**

![image.png](/images/2-preparation/2.1-createvpcec2/image7.png?featherlight=false&width=40pc)

2.4 Ở mục **Instance type** và **Key pair**
- Chọn **t3.medium** bởi vì chúng ta sẽ triển khai chung database server trên Deploy Instance này nên cần nhều bộ nhớ.
- Tạo ra một **key pair** đặt tên là  **devsecops-kp**

![image.png](/images/2-preparation/2.1-createvpcec2/image8.png?featherlight=false&width=40pc)

2.5 Ở mục **Network settings**
- Chọn VPC mà bạn mới tạo
- Chọn **public-subnet-1**
- Bật **Auto-assign public IP**
- Tạo một **Security Group**

![image.png](/images/2-preparation/2.1-createvpcec2/image9.png?featherlight=false&width=30pc)

2.6 Ở mục **Configure storage** thay đổi bộ nhớ thành **30GB** va nhấn **Launch Instance**

![image.png](/images/2-preparation/2.1-createvpcec2/image10.png?featherlight=false&width=60pc)

2.7 Vậy là chúng ta đã hoàn thành việc khởi tạo EC2 Instance.

![image.png](/images/2-preparation/2.1-createvpcec2/image11.png?featherlight=false&width=60pc)