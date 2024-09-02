+++
title = "Connecting to EC2 Instance from VSCode"
date = "`r Sys.Date()`" 
weight = 3
chapter = false
pre = "<b>2.3 </b>"
+++

Connecting via SSH from Visual Studio Code to an EC2 Instance is a quick alternative to using Cloud9.

##### 1. Download Visual Studio Code and the **Remote - SSH** extension

You can download VS Code here: [Download VSCode](https://code.visualstudio.com/download)

Once downloaded, install the **Remote - SSH** extension.

![image.png](/images/2-preparation/2.3-vscodessh/000-connectec2.png?featherlight=false&width=60pc)

##### 2. After installation, click the icon in the bottom left corner to open a dialog box.

![image.png](/images/2-preparation/2.3-vscodessh/001-connectec2.png?featherlight=false&width=60pc)

##### 3. Click on **Connect to Host**.

![image.png](/images/2-preparation/2.3-vscodessh/002-connectec2.png?featherlight=false&width=60pc)

##### 4. Click on **Add New SSH Host**.

![image.png](/images/2-preparation/2.3-vscodessh/003-connectec2.png?featherlight=false&width=60pc)

##### 5. In the input box, enter **deploy-instance-remote** and press Enter.

![image.png](/images/2-preparation/2.3-vscodessh/image.png?featherlight=false&width=60pc)

##### 6. Click the link in **C:\Users\ADMIN\.ssh\config** to configure.

![image.png](/images/2-preparation/2.3-vscodessh/005-connectec2.png?featherlight=false&width=60pc)

![image.png](/images/2-preparation/2.3-vscodessh/image%201.png?featherlight=false&width=60pc)

##### 7. In the SSH configuration block, replace the placeholder with the correct **IPv4 address of the EC2 Instance** and the path to your **Key Pair** on your machine.

```bash
Host deploy-instance-remote
  HostName 3.106.228.72
  User ubuntu
  IdentityFile "C:\Users\ADMIN\Downloads\devsecops-kp.pem"
```
![image.png](/images/2-preparation/2.3-vscodessh/image%202.png?featherlight=false&width=60pc)

##### 8. Click the SSH icon in the bottom left corner to start the Connect process.

![image.png](/images/2-preparation/2.3-vscodessh/image%203.png?featherlight=false&width=60pc)

##### 9. When a new window appears, select **Continue**.

![image.png](/images/2-preparation/2.3-vscodessh/image%204.png?featherlight=false&width=60pc)

##### 10. Click on **Linux** and wait a moment for the VSCode Server to be installed on the EC2 Instance.

![image.png](/images/2-preparation/2.3-vscodessh/008-connectec2.png?featherlight=false&width=60pc)

##### 11. Select **Open Folder** and click **OK**.

![image.png](/images/2-preparation/2.3-vscodessh/image%205.png?featherlight=false&width=60pc)

##### 12. This is the interface after connecting.

![image.png](/images/2-preparation/2.3-vscodessh/image%206.png?featherlight=false&width=60pc)


