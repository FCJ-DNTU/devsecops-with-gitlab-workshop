+++
title = "Tool Installation"
date = "`r Sys.Date()`" 
weight = 4
chapter = false
pre = "<b>2.4 </b>"
+++

### 1. Update and Upgrade Libraries

Switch to root, update and upgrade libraries

```bash
sudo -i
apt update
apt upgrade -y
```
![image.png](/images/2-preparation/2.4-installtools/image.png?featherlight=false&width=60pc)

{{% notice info %}}
If the terminal does not appear, press **Ctrl + `**
{{% /notice %}}

{{% notice info %}}
🡇 Contents 🡇
{{% /notice %}}

If you encounter a kernel upgrade prompt during the process, press OK and then ESC to skip!

![image.png](/images/2-preparation/2.4-installtools/image1.png?featherlight=false&width=60pc)

Alternatively, to disable kernel upgrade notifications, go to

```bash
 vim /etc/needrestart/needrestart.conf
```

And uncomment

```bash
#$nrconf{kernelhints} = -1;
```

Reference link: [21.04 - How to disable "Pending kernel upgrade" message? - Ask Ubuntu](https://askubuntu.com/questions/1349884/how-to-disable-pending-kernel-upgrade-message)

{{% notice info %}}
🡅 Contents 🡅
{{% /notice %}}

### 2. Create Working Directory

```bash
 mkdir tools
 cd tools && mkdir docker dotnet node gitlab cloudbeaver sql
```

![image.png](/images/2-preparation/2.4-installtools/image2.png?featherlight=false&width=60pc)

{{% notice info %}}
You might wonder why the sidebar does not show the created folders when typing commands. This is because we are in root access, and the sidebar only displays files and folders under the ubuntu user.
{{% /notice %}}

### 3. Install Tools

#### 3.1 Install Dotnet 6

Refer to the installation link: [Install .NET on Ubuntu - .NET | Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/core/install/linux-ubuntu-install?tabs=dotnet8&pivots=os-linux-ubuntu-2204)

Create a `setup.sh` file

```bash
vi dotnet/setup.sh
```

Enter the following content

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

Run the command

```bash
sh dotnet/setup.sh
```

Upon completion, you will see the versions

![image.png](/images/2-preparation/2.4-installtools/image3.png?featherlight=false&width=60pc)

#### 3.2 Install Node 18

Refer to the installation link: [How To Install Node.js on Ubuntu 22.04 | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-22-04)

Create a `setup.sh` file

```bash
vi node/setup.sh
```

Enter the content

```bash
#!/bin/bash

# Download and install NVM
echo "### Downloading nvm..."
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash

# Set up environment variable for NVM
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm into the current shell

# Reload shell configuration
source ~/.bashrc

# Check if NVM is installed
nvm -v

# Download and use Node.js version 18
echo "### Downloading Node.js v18..."
nvm install 18
nvm use 18

# Check Node.js and NPM versions
echo "### Checking Node.js and NPM versions..."
node -v
npm -v

# Copy Node.js and NPM to /usr/local for system-wide use
n=$(which node)
n=${n%/bin/node}
sudo cp -r $n/bin/* /usr/local/bin/
sudo cp -r $n/lib/* /usr/local/lib/
if [ -d "$n/share" ]; then sudo cp -r $n/share/* /usr/local/share/; fi

# Check Node.js again after copying
echo "### Checking Node.js after copying..."
node -v
npm -v
```

Run the command

```bash
bash node/setup.sh
```
{{% notice info %}}
Bash is used here because `source ~/.bashrc` is only compatible with bash.
{{% /notice %}}

We get the following result

![image.png](/images/2-preparation/2.4-installtools/image4.png?featherlight=false&width=60pc)

#### 3.3 Install Docker

We need to install Docker to quickly deploy SQL and Cloud Beaver

Refer to the installation link: [How To Install and Use Docker on Ubuntu 22.04 | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04)

Create a `setup.sh` file

```bash
vi docker/setup.sh
```

Enter the content

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

Run the command

```bash
sh docker/setup.sh
```

{{% notice info %}}
🡇 Contents 🡇
{{% /notice %}}

If you receive a **Daemons using outdated libraries** notification

![image.png](/images/2-preparation/2.4-installtools/image5.png?featherlight=false&width=20pc)

You can configure libraries to restart automatically by

```bash
 vim /etc/needrestart/needrestart.conf
```

And changing from

```bash
#$nrconf{restart} = 'i';
```

To

```bash
$nrconf{restart} = 'a'; 
```

Reference link: [linux - How to stop ubuntu pop-up "Daemons using outdated libraries" when using apt to install or update packages? - Stack Overflow](https://stackoverflow.com/questions/73397110/how-to-stop-ubuntu-pop-up-daemons-using-outdated-libraries-when-using-apt-to-i)

{{% notice info %}}
🡅 Contents 🡅
{{% /notice %}}

We now have Docker and Docker Compose

![image.png](/images/2-preparation/2.4-installtools/image6.png?featherlight=false&width=60pc)

#### 3.4 Install SQL

You can refer to the download link here: [How to run SQL Server in a Docker container - LogRocket Blog](https://blog.logrocket.com/docker-sql-server/)

Create a `docker-compose.yml` file

```bash
vi sql/docker-compose.yml
```

Enter the content

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

Run docker-compose

```bash
docker-compose -f sql/docker-compose.yml up -d
```

{{% notice info %}}
If the current location has a `docker-compose.yml` file, the default command will not include the -f flag after `docker-compose -f sql/docker-compose.yml up -d`
{{% /notice %}}

#### 3.5 Install Cloud Beaver

CloudBeaver is a lightweight web application designed for comprehensive data management. It allows you to work with various data sources including SQL, NoSQL, and cloud databases, all through a single secure cloud-based solution accessible via a browser.

You can refer to the download link here: [Run Docker Container · dbeaver/cloudbeaver Wiki (github.com)](https://github.com/dbeaver/cloudbeaver/wiki/Run-Docker-Container)

Create a `docker-compose.yml` file

```bash
vi cloudbeaver/docker-compose.yml
```

Enter the content

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

Run docker-compose

```bash
docker-compose -f cloudbeaver/docker-compose.yml up -d
```

Run the command:

```bash
docker ps
```

All containers should be in the Up state

![image.png](/images/2-preparation/2.4-installtools/image7.png?featherlight=false&width=60pc)

Try accessing Cloud Beaver from the browser at port 8978

![image.png](/images/2-preparation/2.4-installtools/image8.png?featherlight=false&width=60pc)

We have successfully accessed Cloud Beaver. You can interact with the Cloud Beaver interface without needing to use SQL Server CLI commands.

This setup serves as a prerequisite for deploying a Fullstack project including Frontend (ReactJS - Node 18), Backend (.NET 6), and Database (SQL Server).

