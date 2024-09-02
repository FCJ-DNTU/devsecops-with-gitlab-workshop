+++
title = "Create and Connect to Build Instance"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>6.1 </b>"
+++

In this section, we will quickly deploy an additional instance, the Build Instance.

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image.png?featherlight=false&width=60pc)

### 1. Create Build Instance

We will create a Build Instance with options similar to those in [section 2.1](/2-preparation/2.1-createvpcec2), except that since the Build Instance does not need to handle many tasks, set the **Instance type** to t2.micro.

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image1.png?featherlight=false&width=30pc)

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image2.png?featherlight=false&width=30pc)

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image3.png?featherlight=false&width=30pc)

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image4.png?featherlight=false&width=30pc)

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image5.png?featherlight=false&width=30pc)

We now have a Build Instance.

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image6.png?featherlight=false&width=30pc)

### 2. Connect to Deploy Instance

Follow the steps as outlined in [section 2.3](/2-preparation/2.3-vscodessh).

When you get to the Add New SSH Host step, create a new host named `Host build-instance-remote` and update the config file as follows:

```bash
Host build-instance-remote
  HostName 13.236.71.4
  User ubuntu
  IdentityFile "C:\Users\ADMIN\Downloads\devsecops-kp.pem"
```

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image7.png?featherlight=false&width=60pc)

Proceed to connect to the Build Instance.

![image.png](/images/6-cicdon2aec2/6.1-buildinstance/image8.png?featherlight=false&width=60pc)
