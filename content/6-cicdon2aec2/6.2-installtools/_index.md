+++
title = "Install Tools"
date = "`r Sys.Date()`" 
weight = 2
chapter = false
pre = "<b>6.2 </b>"
+++

### 1. Update and Upgrade Libraries

Switch to root, update and upgrade libraries.

```bash
sudo -i
apt update
apt upgrade -y
```

### 2. Create Working Directory

```bash
mkdir tools
cd tools && mkdir docker gitlab
```

### 3. Install Tools

#### 3.1 Install .NET 6

Refer to the installation link: [Install .NET on Ubuntu - .NET | Microsoft Learn](https://learn.microsoft.com/en-us/dotnet/core/install/linux-ubuntu-install?tabs=dotnet8&pivots=os-linux-ubuntu-2204)

Create `setup.sh` file.

```bash
vi dotnet/setup.sh
```

Enter the following content:

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

Run the command:

```bash
sh dotnet/setup.sh
```

#### 3.2 Install Node 18

Refer to the installation link: [How To Install Node.js on Ubuntu 22.04 | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-22-04)

Create `setup.sh` file.

```bash
vi node/setup.sh
```

Enter the content:

```bash
#!/bin/bash

# Download and install NVM
echo "### Downloading nvm..."
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash

# Set up environment variables for NVM
export NVM_DIR="$([ -z "${XDG_CONFIG_HOME-}" ] && printf %s "${HOME}/.nvm" || printf %s "${XDG_CONFIG_HOME}/nvm")"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads NVM into the current shell

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

# Verify Node.js after copying
echo "### Checking Node.js after copying..."
node -v
npm -v
```

Run the command:

```bash
bash node/setup.sh
```

{{% notice info %}}
Use `bash` here because `source ~/.bashrc` is only compatible with `bash`.
{{% /notice %}}

#### 3.3 Install Docker

Refer to the installation link: [How To Install and Use Docker on Ubuntu 22.04 | DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-22-04)

Create `setup.sh` file.

```bash
vi docker/setup.sh
```

Enter the content:

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

Run the command:

```bash
sh docker/setup.sh
```

#### 3.4 Install GitLab Runner

Refer to the installation link: [Install GitLab Runner | GitLab](https://docs.gitlab.com/runner/install/)

Create `setup.sh` file.

```bash
vi gitlab/setup.sh
```

Enter the installation commands:

```bash
#!/bin/bash
apt update -y
curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
apt install gitlab-runner
gitlab-runner --version
```

Run the command:

```bash
sh gitlab/setup.sh
```

### 4. Register GitLab Runner

We will create a GitLab Runner at the Build Instance.

First, go to the group **ecommerce → Build → Runners**.

![image.png](/images/6-cicdon2aec2/6.2-installtools/image11.png?featherlight=false&width=60pc)

Click on "New group runner" to create one.

![image.png](/images/6-cicdon2aec2/6.2-installtools/image12.png?featherlight=false&width=60pc)

Create a new runner with the following details:

- **Tags: group-ecommerce-shell-runner-build**
- Click "Create Runner" to create it.

![image.png](/images/6-cicdon2aec2/6.2-installtools/image.png?featherlight=false&width=60pc)

After creating it, you will get steps to register the runner.

![image.png](/images/6-cicdon2aec2/6.2-installtools/image1.png?featherlight=false&width=60pc)

First, switch to the `gitlab-runner` user so the runner can operate correctly.

```bash
su gitlab-runner
cd
```

![image.png](/images/6-cicdon2aec2/6.2-installtools/image2.png?featherlight=false&width=40pc)

In step 1 and 2, run the commands and fill in the information as shown:

- URL: https://gitlab.com
- Name runner: group-ecommerce-shell-runner-build
- Executor: shell

![image.png](/images/6-cicdon2aec2/6.2-installtools/image3.png?featherlight=false&width=60pc)

The configuration file will be saved at `/home/gitlab-runner/.gitlab-runner/config.toml`.

```bash
vi /home/gitlab-runner/.gitlab-runner/config.toml
```

![image.png](/images/6-cicdon2aec2/6.2-installtools/image14.png?featherlight=false&width=60pc)

Update `concurrent = 2` so the group runner can handle both backend and frontend simultaneously, then save with Esc+ :x.

![image.png](/images/6-cicdon2aec2/6.2-installtools/image13.png?featherlight=false&width=60pc)

In step 3, run:

```bash
nohup gitlab-runner run > start-gitlab-runner.txt 2>&1 &
```

![image.png](/images/6-cicdon2aec2/6.2-installtools/image4.png?featherlight=false&width=60pc)

You will see the GitLab interface showing the runner is connected.

![image.png](/images/6-cicdon2aec2/6.2-installtools/image5.png?featherlight=false&width=60pc)

Thus, the registration is complete!
