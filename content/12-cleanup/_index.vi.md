+++
title = "Dọn dẹp tài nguyên"
date = "`r Sys.Date()`" 
weight = 12
chapter = false
pre = "<b>12. </b>"
+++

### 1. Xóa EC2 Instance

Vào AWS Console   

![image.png](/images/12-cleanup/image.png?featherlight=false&width=60pc)

Chọn Instance muốn xóa và nhấn Terminate

![image.png](/images/12-cleanup/image%201.png?featherlight=false&width=60pc)

### 2. Xóa snapshot (Nếu có)

Nhấn vào Snapshots → Chọn snapshot muốn xóa → Chọn Actions → Delete Snapshot 

![image.png](/images/12-cleanup/image%202.png?featherlight=false&width=60pc)

### 3. Xóa Volume (Nếu Có)

Nhấn vào Volumes → Chọn Volume muốn xóa → Chọn Actions → Chọn Delete Volume

![image.png](/images/12-cleanup/image%203.png?featherlight=false&width=60pc)

### 4. Xóa VPC

Vào Aws console gõ tìm và chọn VPC

![image.png](/images/12-cleanup/image%204.png?featherlight=false&width=60pc)

Nhấn chọn Your VPC → Chọn VPC muốn xóa → Chọn Actions → Chọn Delete VPC

![image.png](/images/12-cleanup/image%205.png?featherlight=false&width=60pc)