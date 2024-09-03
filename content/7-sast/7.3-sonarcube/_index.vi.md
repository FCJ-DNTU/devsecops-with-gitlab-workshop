+++
title = "SonarCube (Self-Host)"
date = "`r Sys.Date()`" 
weight = 3
chapter = false
pre = "<b>7.3 </b>"
+++

Chúng ta sẽ triển khai SonarCube Self-Host tại Build Instance. Bạn có đọc thêm tại đây [Installing from Docker | SonarQube Docs (sonarsource.com)](https://docs.sonarsource.com/sonarqube/latest/setup-and-upgrade/install-the-server/installing-sonarqube-from-docker/)

Đầu tiên tạo thư mục làm việc: 

```bash
cd && mkdir -p /root/tools/sonar && vi /root/tools/sonar/docker-compose.yml
```

Nhập nội dung 

```bash
version: "3"

services:
  sonarqube:
    image: sonarqube:community
    depends_on:
      - db
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://db:5432/sonar
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_logs:/opt/sonarqube/logs
    ports:
      - "9000:9000"
  db:
    image: postgres:12
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
    volumes:
      - postgresql:/var/lib/postgresql
      - postgresql_data:/var/lib/postgresql/data

volumes:
  sonarqube_data:
  sonarqube_extensions:
  sonarqube_logs:
  postgresql:
  postgresql_data:
```

Chạy file docker-compose.yml

```bash
docker-compose up -f tools/sonar/docker-compose.yml up -d
```

Khi gõ lệnh docker ps -a thì chúng ta thấy container sonar-sonarqube-1 bị exit 

![image.png](/images/7-sast/image%2039.png?featherlight=false&width=60pc)

Hãy dùng docker logs để xem lỗi 

![image.png](/images/7-sast/image%2040.png?featherlight=false&width=60pc)

Do Elasticsearch sử dụng nhiều vùng bộ nhớ ảo để ánh xạ (map) các tệp dữ liệu lớn vào bộ nhớ. Nếu giá trị `vm.max_map_count` quá thấp, Elasticsearch sẽ không thể ánh xạ đủ số lượng vùng bộ nhớ cần thiết, dẫn đến các lỗi khi khởi động hoặc trong quá trình hoạt động. Đây chính là nguyên nhân gây ra lỗi "bootstrap checks failed"

Chúng ta sửa lỗi bằng cách tăng max_map_count

```bash
sysctl vm.max_map_count
sudo sysctl -w vm.max_map_count=262144
```

![image.png](/images/7-sast/image%2041.png?featherlight=false&width=60pc)

Sửa file sudo /etc/sysctl.conf/

```bash
sudo nano /etc/sysctl.conf
```

Thêm dòng này vào cuối file

```bash
vm.max_map_count=262144
```

![image.png](/images/7-sast/image%2042.png?featherlight=false&width=60pc)

Khởi động lại docker-compose trong /root/tools/sonar/

```bash

cd /root/tools/sonar/
docker-compose down
docker-compose up -d
```

![image.png](/images/7-sast/image%2043.png?featherlight=false&width=60pc)

Kết quả là đều đã chạy lên

![image.png](/images/7-sast/image%2044.png?featherlight=false&width=60pc)

Chúng ta cần phải cấu hình thêm port 9000 để mà có thể truy cập vào được. 

![image.png](/images/7-sast/image%2045.png?featherlight=false&width=60pc)

Kiểm tra thử port 9000, đây là giao diện chính của **SonarQube**

![image.png](/images/7-sast/image%2046.png?featherlight=false&width=60pc)

Tìm hiểu thêm cách kết nối tại: [(2) GitLab Integration | Mapping your organization into SonarQube - YouTube](https://www.youtube.com/watch?v=XX0ey4rRvms)

Đăng nhập với 

- username: admin
- password: admin

Sau khi đăng nhập cập nhật lại mật khẩu

![image.png](/images/7-sast/image%2047.png?featherlight=false&width=60pc)

Đây là giao diện chính của SonarQube

![image.png](/images/7-sast/image%2048.png?featherlight=false&width=60pc)

- **Configuration name: ecommerce-gitlab-instance**
- **GitLab API URL:** https://gitlab.com/api/v4

![image.png](/images/7-sast/image%2049.png?featherlight=false&width=60pc)

**Personal Access Token** bạn sẽ tạo một token mới trong Edit Profile → Access Tokens → Add new token

![image.png](/images/7-sast/image%2050.png?featherlight=false&width=60pc)

Copy token này dán vào **Personal Access Token** bên SonarQube và nhấn lưu cấu hình

![image.png](/images/7-sast/image%2051.png?featherlight=false&width=60pc)

Tại **Gitlab project onboarding** nhập lại token

![image.png](/images/7-sast/image%2052.png?featherlight=false&width=60pc)

Chọn 2 project frontend và backend để import

![image.png](/images/7-sast/image%2053.png?featherlight=false&width=60pc)

Tại giao diện **Set up 2 projects for Clean as You Code chọn Use the global setting**

![image.png](/images/7-sast/image%2054.png?featherlight=false&width=60pc)

Chúng ta sẽ chọn vào mục Backend trước

![image.png](/images/7-sast/image%2055.png?featherlight=false&width=60pc)

Làm theo hướng dẫn sau

![image.png](/images/7-sast/image%2056.png?featherlight=false&width=60pc)

Tạo token cho project

![image.png](/images/7-sast/image%2057.png?featherlight=false&width=60pc)

Nhấn tạo

![image.png](/images/7-sast/image%2058.png?featherlight=false&width=60pc)

Sao đó copy token

![image.png](/images/7-sast/image%2059.png?featherlight=false&width=60pc)

Chúng ta vào project backend để tạo một biến mới

![image.png](/images/7-sast/image%2060.png?featherlight=false&width=60pc)

Tạo theo hướng dẫn bước 1  

![image.png](/images/7-sast/image%2061.png?featherlight=false&width=60pc)

![image.png](/images/7-sast/image%2062.png?featherlight=false&width=60pc)

Chúng ta đã được 2 biến SONAR_TOKEN và SONAR_HOST_URL

![image.png](/images/7-sast/image%2063.png?featherlight=false&width=60pc)

Chúng ta tiến hành tạo một branch mới là pipeline-be-7.1-sonarqube từ pipeline-be-codelimate trước đó

![image.png](/images/7-sast/image%2064.png?featherlight=false&width=60pc)

Tiến hành edit file gitlab-ci.yaml trong nhánh vừa tạo

![image.png](/images/7-sast/image%2065.png?featherlight=false&width=60pc)

Nhập các câu lệnh sau đây và sau đó commit

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"
    IMAGE_VERSION: "${CI_REGISTRY_USER}/${CI_PROJECT_NAME}-${USER_PROJECT}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}"
    CODECLIMATE_FILE: "${CI_PROJECT_NAME}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}.html"

stages:
    - clone
    - SAST
    - build
    - push registry
    - deploy

before_script:
    - sudo mkdir -p $PATH_PROJECT

clone repository:
    stage: clone
    script:
        - echo "Repository cloned."
    tags:
        - group-ecommerce-shell-runner-build

codelimate testing:
    stage: SAST
    variables:
        GIT_STRATEGY: none
    script:
        - sudo docker run --rm --env CODECLIMATE_CODE="$PWD"  --volume "$PWD":/code  --volume /var/run/docker.sock:/var/run/docker.sock  --volume /tmp/cc:/tmp/cc codeclimate/codeclimate analyze -f html > $CODECLIMATE_FILE
    allow_failure: true
    tags:
        - group-ecommerce-shell-runner-build
    only:
        - tags
    artifacts:
        paths:
        - $CODECLIMATE_FILE
        expire_in: 1 week

sonarqube testing:
  stage: SAST
  cache:
    policy: pull
    key: "${CI_COMMIT_SHORT_SHA}"
    paths:
      - sonar-scanner/
  script:
    - "sudo apt-get update"
    - "sudo apt-get install --yes --no-install-recommends openjdk-17-jre"
    - |
      if ! dotnet tool list -g | grep -q 'dotnet-sonarscanner'; then
        dotnet tool install --global dotnet-sonarscanner
      else
        echo "dotnet-sonarscanner is already installed"
      fi
    - "export PATH=\"$PATH:$HOME/.dotnet/tools\""
    - echo "$SONAR_TOKEN"
    - echo "$SONAR_HOST_URL"
    - "dotnet sonarscanner begin /k:\"ecommerce-fsn_backend_870b5bec-7663-4e18-b150-2f75835d4455\" /d:sonar.token=\"$SONAR_TOKEN\" /d:\"sonar.host.url=$SONAR_HOST_URL\" "
    - "dotnet build"
    - "dotnet sonarscanner end /d:sonar.token=\"$SONAR_TOKEN\""
  allow_failure: true
  tags:
    - group-ecommerce-shell-runner-build
  only:
    - tags

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
    when: manual
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
            sudo docker run --name ${CI_PROJECT_NAME} -dp ${BACKEND_PORT}:${BACKEND_PORT} ${IMAGE_VERSION}"
    after_script:
        - sudo docker logout
    tags:
        - group-ecommerce-shell-runner
    needs:
        - job: dockerhub pushing
    only:
        - tags
```

{{% notice info %}}
Bạn có thể bật qua tab Visualize, và Validate để xem những gì mà nhập trong file gitlab-ci.yaml đã đúng chưa!
{{% /notice %}}

{{% notice info %}}
🡇 Nội dung 🡇
{{% /notice %}}

bạn phải chỉnh sửa lại Project Key trong dòng này thành của bạn để kết quả hoạt động đúng. Nếu bạn thích thì hãy thêm một biến mới bên ngoài để tránh nhầm lẫn

```bash
 - "dotnet sonarscanner begin /k:\"$PROJECT_BACKEND_ID\" /d:sonar.token=\"$SONAR_TOKEN\" /d:\"sonar.host.url=$SONAR_HOST_URL\""
```

Bạn có thể lấy Project Key ở Project Infomation  → Project Key → copy Project Key

![image.png](/images/7-sast/image%2066.png?featherlight=false&width=60pc)

{{% notice info %}}
🡅 Nội dung 🡅
{{% /notice %}}


Chúng ta tạo thêm tag mới để kích hoạt pipeline

![image.png](/images/7-sast/image%2067.png?featherlight=false&width=60pc)

Pipeline đã chạy thành công, bạn có thể nhấp vào tab job dependencies và bật show dependencies để thấy được mối liên hệ giữa các stage. Bạn có thể thấy ở đây bước clone chạy trước để project có thể được tải về, sau đó đến stage SAST sẽ chạy hai bước codelimate testing và sonarqube testing song song. Sau đó bước build được kích hoạt thủ công sau đó dockerhub pushing và tiếp đến là deploy.

![image.png](/images/7-sast/image%2068.png?featherlight=false&width=60pc)

Bước sonarqube testing đã chạy thành công

![image.png](/images/7-sast/image%2069.png?featherlight=false&width=60pc)

{{% notice info %}}
Nếu bạn không chạy được bước sonarqube testing thì hãy chạy thủ công bằng script trong build instance với quyền script.
{{% /notice %}}

Reload lại dự án backend, chúng ta thấy giao diện tổng quan

![image.png](/images/7-sast/image%2070.png?featherlight=false&width=60pc)

Các Issues đang tồn tại

![image.png](/images/7-sast/image%2071.png?featherlight=false&width=60pc)

Những vấn đề bảo mật được liệt kê chi tiết trong đây

![image.png](/images/7-sast/image%2072.png?featherlight=false&width=60pc)

Các đo lường của project

![image.png](/images/7-sast/image%2073.png?featherlight=false&width=60pc)

Và các lỗi ở mã nguồn

![image.png](/images/7-sast/image%2074.png?featherlight=false&width=60pc)

Chúng ta cũng triển khai tương tự ở frontend

Trước hết chúng ta cần cài đặt sonar-scanner trong instance build với quyền gitlab-runner

```bash
# Tải xuống và giải nén sonar-scanner
wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.8.0.2856-linux.zip
unzip sonar-scanner-cli-4.8.0.2856-linux.zip
# Di chuyển sonar-scanner vào thư mục /usr/local để tiện sử dụng
sudo mv sonar-scanner-4.8.0.2856-linux /usr/local/sonar-scanner
export PATH=$PATH:/usr/local/sonar-scanner/bin
# Đảm bảo rằng Java đã được cài đặt trên hệ thống
sudo apt-get update
sudo apt-get install --yes openjdk-17-jre
# Kiểm tra phiên bản
sonar-scanner -v
```

Sau đó trong gitlab-ci.yaml trong dự án frontend nhập dòng lệnh sau:

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"
    IMAGE_VERSION: "${CI_REGISTRY_USER}/${CI_PROJECT_NAME}-${USER_PROJECT}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}"
    CODECLIMATE_FILE: "${CI_PROJECT_NAME}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}.html"

stages:
    - clone
    - SAST
    - build
    - push registry
    - deploy

before_script:
    - sudo mkdir -p $PATH_PROJECT

clone repository:
    stage: clone
    script:
        - echo "Repository cloned."
    tags:
        - group-ecommerce-shell-runner-build

codelimate testing:
    stage: SAST
    variables:
        GIT_STRATEGY: none
    script:
        - sudo docker run --rm --env CODECLIMATE_CODE="$PWD"  --volume "$PWD":/code  --volume /var/run/docker.sock:/var/run/docker.sock  --volume /tmp/cc:/tmp/cc codeclimate/codeclimate analyze -f html > $CODECLIMATE_FILE
    allow_failure: true
    tags:
        - group-ecommerce-shell-runner-build
    only:
        - tags
    artifacts:
        paths:
        - $CODECLIMATE_FILE
        expire_in: 1 week

sonarqube testing:
  stage: SAST
  cache:
    policy: pull
    key: "${CI_COMMIT_SHORT_SHA}"
    paths:
      - sonar-scanner/
  script:
    - "sudo apt-get update"
    - "sudo apt-get install --yes --no-install-recommends openjdk-17-jre"
    - sonar-scanner \
		  -Dsonar.projectKey=ecommerce-fsn_frontend_3cd00dc6-7686-4876-9feb-5526389b35ae \
		  -Dsonar.sources=src \
		  -Dsonar.host.url=$SONAR_HOST_URL \
		  -Dsonar.login=$SONAR_TOKEN
  allow_failure: true
  tags:
    - group-ecommerce-shell-runner-build
  only:
    - tags

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
    when: manual
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
```

Mình đã đi qua cách triển khai SAST với 3 cách tiếp cận khác nhau (CLI, Cloud và Self-Host) với ba công cụ khác nhau. Với mỗi công cụ bạn sẽ tìm và đọc tài liệu mà công cụ đó cung cấp và xem liệu rằng công cụ đó có những cách tiếp cận nào sau đó triển khai vào CI/CD pipeline. Với tư duy này thì bạn sẽ chọn cách tiếp cận phù hợp cho doanh nghiệp của bạn trong từng trường hợp cụ thể nâng cao hiệu suất làm việc và giảm thiểu lỗi trong quá trình code.