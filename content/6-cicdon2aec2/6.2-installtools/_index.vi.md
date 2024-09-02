+++
title = "Cài đặt công cụ"
date = "`r Sys.Date()`" 
weight = 2
chapter = false
pre = "<b>6.2 </b>"
+++

### 1. Cập nhật và nâng cấp các thư viện

Chúng ta chuyển qua root, cập nhật và nâng cấp thư viện 

```bash
sudo -i
apt update
apt upgrade -y
```

### 2. Tạo thư mục làm việc

```bash
 mkdir tools
 cd tools && mkdir docker gitlab
```

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

#### 3.3 Tải Docker

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

#### 3.4 Tải gitlab

Bạn có tham khảo link cài đặt sau: [Install GitLab Runner | GitLab](https://docs.gitlab.com/runner/install/)

Tạo file setup.sh

```bash
vi gitlab/setup.sh
```

Nhập các câu lệnh cài đặt 

```bash
#!/bin/bash
apt update -y
curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
apt install gitlab-runner
gitlab-runner --version
```

Chạy câu lệnh

```bash
sh gitlab/setup.sh
```

### 4. Đăng ký Gitlab Runner

Chúng ta sẽ tạo ra một gitlab-runner nữa tại Build Instance 

Đầu tiên chúng ta vào group **ecommerce → Build → Runners**

![image.png](/images/6-cicdon2aec2/6.2-installtools/image11.png?featherlight=false&width=60pc)

Nhấn vào New group runner để tạo

![image.png](/images/6-cicdon2aec2/6.2-installtools/image12.png?featherlight=false&width=60pc)

Tạo một runner mới

- **Tags: group-ecommerce-shell-runner-build**
- Nhấn nút Create Runner để tạo

![image.png](/images/6-cicdon2aec2/6.2-installtools/image.png?featherlight=false&width=60pc)

Sau khi tạo chúng ta sẽ có các bước để đăng ký runner

![image.png](/images/6-cicdon2aec2/6.2-installtools/image1.png?featherlight=false&width=60pc)

Đầu tiên chúng ta bắc buộc phải chuyển qua quyền của gitlab-runner để runner có thể chạy đúng

```bash
su gitlab-runner
cd
```

![image.png](/images/6-cicdon2aec2/6.2-installtools/image2.png?featherlight=false&width=40pc)

Ở bước 1 và bước chúng copy chạy lệnh và điền thông tin như hình sau:

- URL: https://gitlab.com
- Name runner: group-ecommerce-shell-runner-build
- Executor: shell

![image.png](/images/6-cicdon2aec2/6.2-installtools/image3.png?featherlight=false&width=60pc)

Câu file cấu hình sẽ được lưu tại /home/gitlab-runner/.gitlab-runner/config.toml

```bash
vi /home/gitlab-runner/.gitlab-runner/config.toml
```

![image.png](/images/6-cicdon2aec2/6.2-installtools/image14.png?featherlight=false&width=60pc)

Chúng ta tiến hành sửa concurrent = 2 để group runner có thể đồng thời chạy cả backend lẫn frontend và Esc+ :x để lưu lại

![image.png](/images/6-cicdon2aec2/6.2-installtools/image13.png?featherlight=false&width=60pc)

Bước 3 chúng ta chạy

```bash
nohup gitlab-runner run > start-gitlab-runner.txt 2>&1 &
```

![image.png](/images/6-cicdon2aec2/6.2-installtools/image4.png?featherlight=false&width=60pc)

Chúng ta sẽ thấy bên gitlab sẽ xuất hiện giao diện đã kết nối

![image.png](/images/6-cicdon2aec2/6.2-installtools/image5.png?featherlight=false&width=60pc)

Vậy là chúng ta đã hoàn tất việc đăng ký!