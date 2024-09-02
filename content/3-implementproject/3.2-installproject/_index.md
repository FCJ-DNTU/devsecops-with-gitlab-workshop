+++
title = "Source Code Installation"
date = "`r Sys.Date()`" 
weight = 2
chapter = false
pre = "<b>3.2 </b>"
+++

### 1. Download the Source Code

We will install the source code from the following link: [https://drive.google.com/file/d/1sA8_blpoGf6x0DPbcZsdLhuNHru7Xhxa/view?usp=sharing](https://drive.google.com/file/d/1sA8_blpoGf6x0DPbcZsdLhuNHru7Xhxa/view?usp=sharing)

### 2. Modify the Source Code

After downloading to your machine, unzip the file and navigate to the directory.

![image.png](/images/3-implementproject/3.2-installproject/image.png?featherlight=false&width=60pc)

Access the directory and open a terminal.

![image.png](/images/3-implementproject/3.2-installproject/image1.png?featherlight=false&width=60pc)

Run `code .` to open VSCode.

![image.png](/images/3-implementproject/3.2-installproject/image2.png?featherlight=false&width=60pc)

Make a few modifications to ensure the project functions correctly.

Search for `your_backend_ipv4` in the search tab and replace it with the IPv4 address of the Deploy Instance.

![image.png](/images/3-implementproject/3.2-installproject/image3.png?featherlight=false&width=60pc)

Similarly, search for `your_sqlserver_ipv4` and replace it with the IPv4 address of the Deploy Instance.

![image.png](/images/3-implementproject/3.2-installproject/image4.png?featherlight=false&width=60pc)

Exit VSCode for the `ecommerce-fullstack-netcore-react` project, delete the old zip file, and create a new zip file.

![image.png](/images/3-implementproject/3.2-installproject/image5.png?featherlight=false&width=60pc)

Next, transfer the file directly through VSCode where we have SSH access.

![image.png](/images/3-implementproject/3.2-installproject/image6.png?featherlight=false&width=60pc)

After transferring, a zip file with Ubuntu permissions will appear.

![image.png](/images/3-implementproject/3.2-installproject/image7.png?featherlight=false&width=60pc)

{{% notice info %}}
🡇 Content 🡇
{{% /notice %}}
In addition to dragging files from outside, you can also use the `scp` command:

```bash
scp .\your_file user_name@your_ip:/home/user_name
```

{{% notice info %}}
🡅 Content 🡅
{{% /notice %}}

Once we have the zip file, we will install unzip and extract the ecommerce-fullstack-netcore-react.zip file into a directory at /root.

```bash
apt install unzip && unzip /home/ubuntu/ecommerce-fullstack-netcore-react.zip /root
```

After unzipping, you will have a directory like this.

![image.png](/images/3-implementproject/3.2-installproject/image8.png?featherlight=false&width=60pc)