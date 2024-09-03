+++
title = "Clean Up Resources"
date = "`r Sys.Date()`" 
weight = 12
chapter = false
pre = "<b>12. </b>"
+++

### 1. Delete EC2 Instance

Go to the AWS Console   

![image.png](/images/12-cleanup/image.png?featherlight=false&width=60pc)

Select the instance you want to delete and click Terminate.

![image.png](/images/12-cleanup/image%201.png?featherlight=false&width=60pc)

### 2. Delete Snapshot (If any)

Click on Snapshots → Select the snapshot you want to delete → Choose Actions → Delete Snapshot 

![image.png](/images/12-cleanup/image%202.png?featherlight=false&width=60pc)

### 3. Delete Volume (If any)

Click on Volumes → Select the volume you want to delete → Choose Actions → Delete Volume

![image.png](/images/12-cleanup/image%203.png?featherlight=false&width=60pc)

### 4. Delete VPC

Go to the AWS Console, search for and select VPC.

![image.png](/images/12-cleanup/image%204.png?featherlight=false&width=60pc)

Click on Your VPC → Select the VPC you want to delete → Choose Actions → Delete VPC

![image.png](/images/12-cleanup/image%205.png?featherlight=false&width=60pc)
