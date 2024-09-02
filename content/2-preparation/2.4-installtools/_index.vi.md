+++
title = "Cài đặt công cụ"
date = "`r Sys.Date()`" 
weight = 4
chapter = false
pre = "<b>2.4 </b>"
+++

### 1. Cập nhật và nâng cấp các thư viện

Chúng ta chuyển qua root, cập nhật và nâng cấp thư viện 

```bash
sudo -i
apt update
apt upgrade -y
```
![image.png](/images/2-preparation/2.4-installtools/image.png?featherlight=false&width=60pc)

{{% notice info %}}
Nếu mà bạn không thấy terminal hiện lên thì hãy nhấn tổ hợp phím **Ctrl + `**
{{% /notice %}}

{{% notice info %}}
🡇 Nội dung 🡇
{{% /notice %}}

Nếu trong quá trình làm mà các bạn thấy câu lệnh nâng cấp kernel thì hãy nhấn OK rồi ESC để bỏ qua nhé!

![image.png](/images/2-preparation/2.4-installtools/image1.png?featherlight=false&width=60pc)

Hoặc một cách khác để tắt thông báo nâng cấp kernel bạn vào 

```bash
 vim /etc/needrestart/needrestart.conf
```

Và uncomment 

```bash
#$nrconf{kernelhints} = -1;
```

Link tham khảo: [21.04 - How to disable "Pending kernel upgrade" message? - Ask Ubuntu](https://askubuntu.com/questions/1349884/how-to-disable-pending-kernel-upgrade-message)

{{% notice info %}}
🡅 Nội dung 🡅
{{% /notice %}}

### 2. Tạo thư mục làm việc

```bash
 mkdir tools
 cd tools && mkdir docker dotnet node gitlab cloudbeaver sql
```

![image.png](/images/2-preparation/2.4-installtools/image2.png?featherlight=false&width=60pc)

{{% notice info %}}
Có thể bạn sẽ thắc mắc tại sao thanh sidebar không có thư mục được tạo khi mà chúng ta gõ lệnh trên. Mình xin giải thích là do chúng ta đã truy cập vào quyền root rồi nhé. Còn thanh sidebar chỉ đang hiển thị các tập tin và thư mục dưới quyền ubuntu thôi!
{{% /notice %}}

### 3. Cài đặt các công cụ

#### 3.1 Tải Dotnet 6

Bạn có tham khảo link cài đặt sau: [Install .NET on Ubuntu - .NET | Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/core/install/linux-ubuntu-install?tabs=dotnet8&pivots=os-linux-ubuntu-2204)

Tạo file setup.sh

```bash
vi dotnet/setup.sh
```

Nhập nội dung sau đây

```bash
#!/bin/bash
sudo apt-get update
sudo apt-get install -y dotnet-sdk-6.0
sudo apt-get update
sudo apt-get install -y aspnetcore-runtime-6.0
# Version sdks
dotnet --list-sdks
# Version runtimes
dotnet --list-runtimes
# Version dotnet 
dotnet --version
```

Chạy câu lệnh

```bash
sh dotnet/setup.sh
```

Khi cài đặt hoàn tất bạn sẽ thấy được các phiên bản

![image.png](/images/2-preparation/2.4-installtools/image3.png?featherlight=false&width=60pc)

#### 3.2 Tải Node 18

Bạn có tham khảo link cài đặt sau: [How To Install Node.js on Ubuntu 22.04 | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-22-04)

Tạo file setup.sh

```bash
vi node/setup.sh
```

Nhập nội dung

```bash
#!/bin/bash

# Tải và cài đặt NVM
echo "### Downloading nvm..."
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash

# Thiết lập biến môi trường cho NVM
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # Điều này nạp NVM vào shell hiện tại

# Tải lại cấu hình shell
source ~/.bashrc

# Kiểm tra NVM đã được cài đặt chưa
nvm -v

# Tải và sử dụng Node.js phiên bản 18
echo "### Downloading Node.js v18..."
nvm install 18
nvm use 18

# Kiểm tra phiên bản Node.js và NPM
echo "### Checking Node.js and NPM versions..."
node -v
npm -v

# Sao chép Node.js và NPM ra thư mục /usr/local để dùng toàn hệ thống
n=$(which node)
n=${n%/bin/node}
sudo cp -r $n/bin/* /usr/local/bin/
sudo cp -r $n/lib/* /usr/local/lib/
if [ -d "$n/share" ]; then sudo cp -r $n/share/* /usr/local/share/; fi

# Kiểm tra lại Node.js sau khi sao chép
echo "### Checking Node.js after copying..."
node -v
npm -v
```

Chạy câu lệnh

```bash
bash node/setup.sh
```
{{% notice info %}}
Ở đây sử dụng bash bởi vì  `source ~/.bashrc` chỉ tương thích khi chạy với bash.
{{% /notice %}}

Ta được kết quả như sau

![image.png](/images/2-preparation/2.4-installtools/image4.png?featherlight=false&width=60pc)

#### 3.3 Tải Docker

Chúng ta sẽ cần tải Docker để triển khai nhanh chóng Sql và Cloud Beaver

Bạn có tham khảo link cài đặt sau: [How To Install and Use Docker on Ubuntu 22.04 | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04)

Tạo file setup.sh

```bash
vi docker/setup.sh
```

Nhập nội dung

```bash
#!/bin/bash
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update -y
sudo apt install docker-ce -y
sudo systemctl start docker
sudo systemctl enable docker
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker -v
docker-compose -v
```

Chạy câu lệnh

```bash
sh docker/setup.sh
```

{{% notice info %}}
🡇 Nội dung 🡇
{{% /notice %}}

Nếu bạn nhận được thông báo **Daemons using outdated libraries**

![image.png](/images/2-preparation/2.4-installtools/image5.png?featherlight=false&width=20pc)

Bạn có thể tùy chỉnh cho các thư viện restart tự động bằng cách

```bash
 vim /etc/needrestart/needrestart.conf
```

Và đổi từ dòng

```bash
#$nrconf{restart} = 'i';
```

Thành dòng này

```bash
$nrconf{restart} = 'a'; 
```

Link tham khảo: [linux - How to stop ubuntu pop-up "Daemons using outdated libraries" when using apt to install or update packages? - Stack Overflow](https://stackoverflow.com/questions/73397110/how-to-stop-ubuntu-pop-up-daemons-using-outdated-libraries-when-using-apt-to-i)

{{% notice info %}}
🡅 Nội dung 🡅
{{% /notice %}}

Chúng ta đã có docker và docker-compose

![image.png](/images/2-preparation/2.4-installtools/image6.png?featherlight=false&width=60pc)

#### 3.4 Tải SQL

Bạn có thể tham khảo link tải ở đây: [How to run SQL Server in a Docker container - LogRocket Blog](https://blog.logrocket.com/docker-sql-server/)

Tạo file docker-compose.yml

```bash
vi sql/docker-compose.yml
```

Nhập nội dung 

```bash
version: '3.8'

services:
  sqlserver:
    image: mcr.microsoft.com/mssql/server
    container_name: sqlserver
    restart: always
    ports:
      - "1433:1433"
    environment:
      ACCEPT_EULA: "Y"
      SA_PASSWORD: "Str0ngPa5sVvorcl"
    volumes:
      - sqlserver-data:/var/opt/mssql

volumes:
  sqlserver-data:
    driver: local
```

Chạy docker-compose

```bash
docker-compose -f sql/docker-compose.yml up -d
```

{{% notice info %}}
Nếu mà vị trí hiện tại có file docker-compose.yml thì mặc định câu lệnh sẽ không có flag -f sau câu lệnh **`docker-compose -f sql/docker-compose.yml up -d`**
{{% /notice %}}

#### 3.5 Tải Cloud Beaver

CloudBeaver là một ứng dụng web nhẹ được thiết kế để quản lý dữ liệu toàn diện. Nó cho phép bạn làm việc với nhiều nguồn dữ liệu khác nhau, bao gồm SQL, NoSQL và cơ sở dữ liệu đám mây, tất cả đều thông qua một giải pháp đám mây an toàn duy nhất có thể truy cập qua trình duyệt.

Bạn có thể tham khảo link tải ở đây: [Run Docker Container · dbeaver/cloudbeaver Wiki (github.com)](https://github.com/dbeaver/cloudbeaver/wiki/Run-Docker-Container)

Tạo file docker-compose.yml

```bash
vi c**loudbeaver**/docker-compose.yml
```

Nhập nôi dụng sau:

```bash
version: '3.8'

services:
  cloudbeaver:
    image: dbeaver/cloudbeaver
    container_name: cloudbeaver
    restart: always
    ports:
      - "8978:8978"
    volumes:
      - cloudbeaver-data:/opt/cloudbeaver/workspace

volumes:
  cloudbeaver-data:
    driver: local
```

Chạy docker-compose 

```bash
docker-compose -f c**loudbeaver**/docker-compose.yml up -d
```

Chúng ta sẽ chạy lệnh:

```bash
docker ps
```

Trang thái của các container đều Up

![image.png](/images/2-preparation/2.4-installtools/image7.png?featherlight=false&width=60pc)

Thử truy cập Cloud Beaver từ browser port 8978

![image.png](/images/2-preparation/2.4-installtools/image8.png?featherlight=false&width=60pc)

Chúng ta đã truy cập thành công vào Cloud Beaver. Chúng ta có thể thao tác trên giao diện Cloud Beaver mà không cần gõ các lệnh CLI của Sql Server.

Đây là tiên đề để chúng triển khai dự án Fullstack gồm có Frontend (ReactJS - Node 18), Backend (.NET 6) và Database (SQL Server).