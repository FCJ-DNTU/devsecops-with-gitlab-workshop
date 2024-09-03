+++
title = "Implementing DAST with Arachni"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>10.1 </b>"
+++

Summary of DAST tools:
[Best Vulnerability Scanner Software: User Reviews from August 2024 (g2.com)](https://www.g2.com/categories/vulnerability-scanner)

Arachni tool link:
[Arachni/arachni (github.com)](https://github.com/Arachni/arachni)

#### 1. Create a New User

Since Arachni cannot run with root privileges, we will create a new user for Arachni on the Build Instance.

```bash
adduser arachni
```

![image.png](/images/10-dast/image.png?featherlight=false&width=60pc)

Switch to the arachni user.

```bash
su arachni && cd
```

#### 2. Install the Tool and Run Manually

You can refer to the releases here: [Releases · Arachni/arachni (github.com)](https://github.com/Arachni/arachni/releases)

Download Arachni:

```bash
wget https://github.com/Arachni/arachni/releases/download/v1.4/arachni-1.4-0.5.10-linux-x86_64.tar.gz
```

![image.png](/images/10-dast/image%201.png?featherlight=false&width=60pc)

Extract the downloaded file:

```bash
tar -xvf arachni-1.4-0.5.10-linux-x86_64.tar.gz
```

![image.png](/images/10-dast/image%202.png?featherlight=false&width=60pc)

Navigate to the directory:

```bash
cd arachni-1.4-0.5.10
```

You will find many commands in the bin folder.

```bash
ls bin/
```

![image.png](/images/10-dast/image%203.png?featherlight=false&width=60pc)

Build the .arf file. You can learn more here: [Command line user interface · Arachni/arachni Wiki (github.com)](https://github.com/Arachni/arachni/wiki/Command-line-user-interface)

```bash
bin/arachni --output-verbose --scope-include-subdomains http://3.106.228.72:3000/ --report-save-path=/tmp/frontend-ecommerce.arf
```

![image.png](/images/10-dast/image%204.png?featherlight=false&width=60pc)


{{% notice info %}}
You must replace placeholders with your own variables to get accurate results!
{{% /notice %}}

This is the result of the security scan. After scanning, a file frontend-ecommerce.arf is created in the /tmp directory.

![image.png](/images/10-dast/image%205.png?featherlight=false&width=60pc)

Convert the .arf file to an .html file so it can be read:

```bash
bin/arachni_reporter /tmp/frontend-ecommerce.arf --reporter=html:outfile=frontend-ecommerce.html.zip
```

The result is a .zip file.

![image.png](/images/10-dast/image%206.png?featherlight=false&width=60pc)

To view it, switch to root and copy it to the /home/ubuntu/ directory:

```bash
exit
cp /home/arachni/arachni-1.4-0.5.10/frontend-ecommerce.html.zip /home/ubuntu/
```

You will see a .zip file has been copied. Grant permissions to ubuntu, download, and open the .html file:

```bash
sudo chown -R ubuntu:ubuntu /home/ubuntu/frontend-ecommerce.html.zip
```

![image.png](/images/10-dast/image%207.png?featherlight=false&width=60pc)

This is the analysis result from Arachni.

![image.png](/images/10-dast/image%208.png?featherlight=false&width=60pc)

#### 3. Write a Dockerfile

Now we will write a Dockerfile to simplify the process.

Switch to the arachni user:

```bash
su arachni
cd
```

Create a working directory and Dockerfile:

```bash
mkdir -p tools/arachni && vi tools/arachni/Dockerfile
```

Enter the following lines:

```bash
FROM ubuntu:latest

RUN apt update -y && \
    apt install -y wget tar && \
    rm -rf /var/lib/apt/lists/*

RUN wget https://github.com/Arachni/arachni/releases/download/v1.4/arachni-1.4-0.5.10-linux-x86_64.tar.gz && \
    tar -xvf arachni-1.4-0.5.10-linux-x86_64.tar.gz && \
    rm arachni-1.4-0.5.10-linux-x86_64.tar.gz

WORKDIR /arachni-1.4-0.5.10

CMD ["bin/arachni"]
```

Switch to root and add the arachni user to the docker group:

```bash
exit
usermod -aG docker arachni
```

Add the following line to visudo:

```bash
arachni ALL=(ALL) NOPASSWD:ALL
```

![image.png](/images/10-dast/image%209.png?featherlight=false&width=60pc)

Switch back to arachni and build the Dockerfile into a Docker image:

```bash
su arachni
cd
docker build -t arachni tools/arachni/
```

![image.png](/images/10-dast/image%2010.png?featherlight=false&width=60pc)

Run Arachni using Docker with the same command as before:

```bash
docker run --rm -v /tmp/:/tmp/ arachni bin/arachni --output-verbose --scope-include-subdomains http://3.106.228.72:3000/ --report-save-path=/tmp/frontend-ecommerce.arf
```

![image.png](/images/10-dast/image%2011.png?featherlight=false&width=60pc)

Also, convert the .arf file to .html using Docker:

```bash
docker run --rm -v /tmp/:/tmp/ arachni bin/arachni_reporter /tmp/frontend-ecommerce.arf --reporter=html:outfile=/tmp/frontend-ecommerce.html.zip
```

And the results are the same.

![image.png](/images/10-dast/image%2012.png?featherlight=false&width=60pc)

#### 4. Apply to CI/CD Frontend

![image.png](/images/10-dast/image%2013.png?featherlight=false&width=60pc)

![image.png](/images/10-dast/image%2014.png?featherlight=false&width=60pc)

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"
    IMAGE_VERSION: "${CI_REGISTRY_USER}/${CI_PROJECT_NAME}-${USER_PROJECT}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}"
    ARACHNI_WEBSITE_REPORT: "arachni_scan_${CI_PROJECT_NAME}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}_report"

stages:
    - clone
    - SAST
    - SCA
    - build
    - image can
    - push registry
    - deploy
    - DAST

before_script:
    - sudo mkdir -p $PATH_PROJECT

clone repository:
    stage: clone
    script:
        - echo "Repository cloned."
    tags:
        - group-ecommerce-shell-runner-build

build:
    stage: build
    variables:
        GIT_STRATEGY: clone
    before_script:
        - sudo docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PWD
    script:
        - sudo docker build -t $IMAGE_VERSION .
    after_script:
        - sudo docker logout
    tags:
        - group-ecommerce-shell-runner-build
    only:
        - tags

dockerhub pushing:
    stage: push registry
    variables:
        GIT_STRATEGY: none
    before_script:
        - sudo docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PWD
    script:
        - sudo docker push $IMAGE_VERSION
    after_script:
        - sudo docker logout
    tags:
        - group-ecommerce-shell-runner-build
    needs:
        - job: build
    only:
        - tags

deploy:
    stage: deploy
    variables:
        GIT_STRATEGY: none
    before_script:
        - sudo docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PWD
    script:
        - sudo docker pull $IMAGE_VERSION
        - sudo su ${USER_PROJECT} -c "
            container_exists=\$(sudo docker ps -a -q -f name=${CI_PROJECT_NAME});
            if [ ! -z \"\$container_exists\" ]; then
                sudo docker rm -f ${CI_PROJECT_NAME};
            fi;
            sudo docker run --name ${CI_PROJECT_NAME} -dp ${FRONTEND_PORT}:80 ${IMAGE_VERSION}"
    after_script:
        - sudo docker logout
    tags:
        - group-ecommerce-shell-runner
    needs:
        - job: dockerhub pushing
    only:
        - tags

arachni scanning website:
    stage: DAST
    variables:
        GIT_STRATEGY: none
    before_script:
        - sudo docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PWD
    script:
        - docker run --rm -v /tmp/:/tmp/ arachni bin/arachni --output-verbose --scope-include-subdomains http://3.106.228.72:3000/ --report-save-path=/tmp/$ARACHNI_WEBSITE_REPORT.arf > /dev/null 2>&1
        - docker run --rm -v /tmp/:/tmp/ arachni bin/arachni_reporter /tmp/frontend-ecommerce.arf --reporter=html:outfile=/tmp/$ARACHNI_WEBSITE_REPORT.html.zip
        - sudo chmod 777 /tmp/$ARACHNI_WEBSITE_REPORT.html.zip
        - cp /tmp/$ARACHNI_WEBSITE_REPORT.html.zip .
    after_script:
        - sudo docker logout
    tags:
        - group-ecommerce-shell-runner-build
    needs:
        - job: deploy
    only:
        - tags
    artifacts:
        paths:
            - $ARACHNI_WEBSITE_REPORT.html.zip
        expire_in: 1 day
```

{{% notice info %}}
To simplify, unnecessary stages have been removed to keep it lean, and only essential stages such as clone, build, push registry, deploy, and the new DAST stage have been kept.
{{% /notice %}}

![image.png](/images/10-dast/image%2015.png?featherlight=false&width=60pc)

![image.png](/images/10-dast/image%2016.png?featherlight=false&width=60pc)

Download and extract the results, which remain the same.

![image.png](/images/10-dast/image%2017.png?featherlight=false&width=60pc)
