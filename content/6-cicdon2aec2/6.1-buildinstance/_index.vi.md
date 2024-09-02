+++
title = "Tạo và kết nối đến Build Instance"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>6.1 </b>"
+++

Trong phần này chúng ta sẽ triển khai nhanh thêm một Instance nữa là Build Instance

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image.png?featherlight=false&width=60pc)

### 1. Tạo Build Instance

Chúng ta sẽ tạo thêm một Build Instance với các tùy chọn giống như phần tạo Deploy Instance ở [mục 2.1](/2-preparation/2.1-createvpcec2)  chỉ khác là ở Build Instance giống không cần xử lý tác vụ nhiều nên để **Instance type** là t2.micro

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image1.png?featherlight=false&width=30pc)

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image2.png?featherlight=false&width=30pc)

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image3.png?featherlight=false&width=30pc)

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image4.png?featherlight=false&width=30pc)

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image5.png?featherlight=false&width=30pc)

Chúng ta đã có Build Intance

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image6.png?featherlight=false&width=30pc)

### 2. Kết nối đến Deploy Instance

Chúng ta sẽ làm từng bước như ở [mục 2.3](/2-preparation/2.3-vscodessh)

Đến bước Add New SSH Host thì tạo một host mới có tên là Host build-instance-remote và vào file config  

```bash
Host build-instance-remote
  HostName 13.236.71.4
  User ubuntu
  IdentityFile "C:\Users\ADMIN\Downloads\devsecops-kp.pem"
```

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image7.png?featherlight=false&width=60pc)

Tiến hành kết nối đến Build Instance 

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image8.png?featherlight=false&width=60pc)