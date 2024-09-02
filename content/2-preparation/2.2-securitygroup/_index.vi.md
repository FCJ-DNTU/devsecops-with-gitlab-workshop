+++
title = "Cấu hình Security Group"
date = "`r Sys.Date()`" 
weight = 2
chapter = false
pre = "<b>2.2 </b>"
+++

#### 1. Vào giao diện EC2 Instance
- Chọn Tab Security
- Chọn Security Groups

![image.png](/images/2-preparation/2.2-securitygroup/image.png?featherlight=false&width=60pc)

#### 2. Tại giao diện Security Groups

Nhấp chọn **Inbound rule** → **Edit inbound rule** 

Tạo SSH

- Type: **SSH**
- Source: **My IP**
- Description: **SSH From my IP**

Tạo Custom TCP

- Type: **Custom TCP**
- Port range: **8978**
- Source: **My IP**
- Description: **Access from my IP to Cloud Beaver**

Tạo Custom TCP

- Type: MSSQL
- Source: **My IP**
- Description: Access from my IP to SQL Server

Tạo Custom TCP

- Type: MSSQL
- Source: **Ipv4 EC2 deploy Instance của bạn**
- Description: Access from Cloud Beaver to SQL Server

Tạo Custom TCP

- Type: **Custom TCP**
- Port range: **5214**
- Source: **My IP**
- Description: **Access from my IP to Backend .NET**

Tạo Custom TCP

- Type: **Custom TCP**
- Port range: **3000**
- Source: **Anywhere-IPv4**
- Description: **Access to Frontend ReactJS**

![image.png](/images/2-preparation/2.2-securitygroup/image1.png?featherlight=false&width=60pc)