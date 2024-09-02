+++
title = "Configuring Security Group"
date = "`r Sys.Date()`" 
weight = 2
chapter = false
pre = "<b>2.2 </b>"
+++

#### 1. Access the EC2 Instance Interface
- Select the **Security** tab
- Choose **Security Groups**

![image.png](/images/2-preparation/2.2-securitygroup/image.png?featherlight=false&width=60pc)

#### 2. In the Security Groups Interface

Click **Inbound rules** → **Edit inbound rules**

Create SSH rule:
- **Type**: **SSH**
- **Source**: **My IP**
- **Description**: **SSH From my IP**

Create Custom TCP rule:
- **Type**: **Custom TCP**
- **Port range**: **8978**
- **Source**: **My IP**
- **Description**: **Access from my IP to Cloud Beaver**

Create Custom TCP rule:
- **Type**: **MSSQL**
- **Source**: **My IP**
- **Description**: **Access from my IP to SQL Server**

Create Custom TCP rule:
- **Type**: **MSSQL**
- **Source**: **IPv4 EC2 deploy Instance of your choice**
- **Description**: **Access from Cloud Beaver to SQL Server**

Create Custom TCP rule:
- **Type**: **Custom TCP**
- **Port range**: **5214**
- **Source**: **My IP**
- **Description**: **Access from my IP to Backend .NET**

Create Custom TCP rule:
- **Type**: **Custom TCP**
- **Port range**: **3000**
- **Source**: **Anywhere-IPv4**
- **Description**: **Access to Frontend ReactJS**

![image.png](/images/2-preparation/2.2-securitygroup/image1.png?featherlight=false&width=60pc)
