+++
title = "Triển khai DAST với Arachni"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>10.1 </b>"
+++

Tổng hợp các công cụ DAST:
[Best Vulnerability Scanner Software: User Reviews from August 2024 (g2.com)](https://www.g2.com/categories/vulnerability-scanner)

Đường đẫn công cụ Arachni:
[Arachni/arachni(github.com)](https://github.com/Arachni/arachni)

#### 1. Tạo user mới

Do Arachni không cho chạy với quyền root nên là chúng ta sẽ tạo mới một user cho Arachni tại Build Instance 

```bash
adduser arachni
```

![image.png](/images/10-dast/image.png?featherlight=false&width=60pc)

Chuyển qua user arachni

```bash
su arachni && cd
```

#### 2. Cài đặt công cụ và chạy thủ công

Bạn có thể tham khảo các release tại đây: [Releases · Arachni/arachni (github.com)](https://github.com/Arachni/arachni/releases)

Tải Arachni

```bash
wget https://github.com/Arachni/arachni/releases/download/v1.4/arachni-1.4-0.5.10-linux-x86_64.tar.gz
```

![image.png](/images/10-dast/image%201.png?featherlight=false&width=60pc)

Chúng ta giải nén file mới vừa tải về

```bash
	tar -xvf arachni-1.4-0.5.10-linux-x86_64.tar.gz
```

![image.png](/images/10-dast/image%202.png?featherlight=false&width=60pc)

Di chuyển vào thư mục 

```bash
cd arachni-1.4-0.5.10
```

Chúng ta thấy có rất nhiều câu lệnh trong bin

```bash
ls bin/
```

![image.png](/images/10-dast/image%203.png?featherlight=false&width=60pc)

Tiến hành build ra file .arf bạn có thể tìm hiểu tại đây: [Command line user interface · Arachni/arachni Wiki (github.com)](https://github.com/Arachni/arachni/wiki/Command-line-user-interface)

```bash
 bin/arachni --output-verbose --scope-include-subdomains http://3.106.228.72:3000/ --report-save-path=/tmp/frontend-ecommerce.arf
```

![image.png](/images/10-dast/image%204.png?featherlight=false&width=60pc)


{{% notice info %}}
Bạn phải thay đổi thành biến của bạn để cho ra kết quả đúng!
{{% /notice %}}

Đây là kết quả của việc quét bảo mật. Sau khi quét sinh ra một file frontend-ecommerce.arf trong thư mục /tmp

![image.png](/images/10-dast/image%205.png?featherlight=false&width=60pc)

Chúng ta chuyển file .arf thành file .html để chúng ta có thể đọc 

```bash
bin/arachni_reporter /tmp/frontend-ecommerce.arf --reporter=html:outfile=frontend-ecommerce.html.zip
```

Kết quả sinh ra một file .zip

![image.png](/images/10-dast/image%206.png?featherlight=false&width=60pc)

Để có thể xem được chúng ta chuyển qua quyền root và copy sang thư mục /home/ubuntu/

```bash
 exit
 cp /home/arachni/arachni-1.4-0.5.10/frontend-ecommerce.html.zip /home/ubuntu/
```

Chúng ta thấy có một file .zip được copy, chúng ta tiến hành cấp quyền cho ubuntu, tải về và mở file .html 

```bash
sudo chown -R ubuntu:ubuntu /home/ubuntu/frontend-ecommerce.html.zip
```

![image.png](/images/10-dast/image%207.png?featherlight=false&width=60pc)

Đây là kết quả phân tích của Arachni

![image.png](/images/10-dast/image%208.png?featherlight=false&width=60pc)

#### 3. Viết Dockerfile

Bây giờ chúng ta sẽ viết Dockerfile để tiện hơn trong việc chạy lên 

Chuyển qua quyền arachni

```bash
su arachni
cd
```

Sau đó tạo thư mục làm việc và tạo Dockerfile

```bash
mkdir -p tools/arachni && vi tools/arachni/Dockerfile
```

Nhập dòng sau

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

Tiến hành chuyển qua quyền root và add group cho arachni

```bash
exit
usermod -aG docker arachni
```

Thêm dòng sau vào visudo 

```bash
arachni ALL=(ALL) NOPASSWD:ALL
```

![image.png](/images/10-dast/image%209.png?featherlight=false&width=60pc)

Chuyển qua arachni và Build Dockerfile thành docker image 

```bash
su arachni
cd
docker build -t arachni tools/arachni/
```

![image.png](/images/10-dast/image%2010.png?featherlight=false&width=60pc)

Chúng ta tiến hành chạy arachni giống câu lệnh lúc nãy chỉ khác bây giờ dùng docker run

```bash
docker run --rm -v /tmp/:/tmp/ arachni bin/arachni --output-verbose --scope-include-subdomains http://3.106.228.72:3000/ --report-save-path=/tmp/frontend-ecommerce.arf
```

![image.png](/images/10-dast/image%2011.png?featherlight=false&width=60pc)

Chúng ta cũng chuyển file .afr thành file .html

```bash
docker run --rm -v /tmp/:/tmp/ arachni bin/arachni_reporter /tmp/frontend-ecommerce.arf --reporter=html:outfile=/tmp/frontend-ecommerce.html.zip
```

Và kết quả vẫn như cũ

![image.png](/images/10-dast/image%2012.png?featherlight=false&width=60pc)

#### 4. Áp dung cho CI/CD frontend

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
Do để tối giản chúng ta xóa đi hết những stage không cần thiết để gọn hơn và giữ lại những stage thiết yếu như clone, build, push registry, deploy và thêm phần mới là DAST.
{{% /notice %}}

![image.png](/images/10-dast/image%2015.png?featherlight=false&width=60pc)

![image.png](/images/10-dast/image%2016.png?featherlight=false&width=60pc)

Tải về và giải nén kết quả vẫn được như cũ 

![image.png](/images/10-dast/image%2017.png?featherlight=false&width=60pc)