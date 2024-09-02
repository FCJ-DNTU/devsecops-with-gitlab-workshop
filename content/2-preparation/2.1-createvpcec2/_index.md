+++
title = "Creating VPC and EC2 Instance"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>2.1 </b>"
+++

### 1. Creating a VPC

1.1 Access the [AWS Management Console](https://aws.amazon.com/vi/console/)
- Search for **VPC**
- Select **Create VPC**

![image.png](/images/2-preparation/2.1-createvpcec2/image.png?featherlight=false&width=60pc)

1.2 In the **VPC settings** interface:
- **Resources to create**, choose **VPC and more**
- **Name tag auto-generation**, enter **Devsecops VPC**
- **IPv4 CIDR block**, enter **10.0.0.0/16**

![image.png](/images/2-preparation/2.1-createvpcec2/image1.png?featherlight=false&width=30pc)

1.3 Select the AZs as shown and click **Create VPC**

![image.png](/images/2-preparation/2.1-createvpcec2/image2.png?featherlight=false&width=30pc)

1.4 After creation, you will have a VPC like this:

![image.png](/images/2-preparation/2.1-createvpcec2/image3.png?featherlight=false&width=60pc)

### 2. Creating an EC2 Instance

2.1 Access the [AWS Management Console](https://aws.amazon.com/vi/console/)
- Search for **EC2**

    ![image.png](/images/2-preparation/2.1-createvpcec2/image4.png?featherlight=false&width=60pc)

- Select **Launch Instance**

    ![image.png](/images/2-preparation/2.1-createvpcec2/image5.png?featherlight=false&width=60pc)

2.2 In the **Launch an instance** interface:
- **Name and tags**: Deploy Instance

![image.png](/images/2-preparation/2.1-createvpcec2/image6.png?featherlight=false&width=60pc)

2.3 In the **Application and OS Images (Amazon Machine Image)** section:
- Choose **Ubuntu Server 22.04 LTS (HVM), SSD Volume Type**

![image.png](/images/2-preparation/2.1-createvpcec2/image7.png?featherlight=false&width=40pc)

2.4 In the **Instance type** and **Key pair** sections:
- Choose **t3.medium** because we will deploy the database server on this instance, so more memory is needed.
- Create a **key pair** named **devsecops-kp**

![image.png](/images/2-preparation/2.1-createvpcec2/image8.png?featherlight=false&width=40pc)

2.5 In the **Network settings** section:
- Select the VPC you just created
- Choose **public-subnet-1**
- Enable **Auto-assign public IP**
- Create a **Security Group**

![image.png](/images/2-preparation/2.1-createvpcec2/image9.png?featherlight=false&width=30pc)

2.6 In the **Configure storage** section, change the storage size to **30GB** and click **Launch Instance**

![image.png](/images/2-preparation/2.1-createvpcec2/image10.png?featherlight=false&width=60pc)

2.7 We have now completed the creation of the EC2 Instance.

![image.png](/images/2-preparation/2.1-createvpcec2/image11.png?featherlight=false&width=60pc)
