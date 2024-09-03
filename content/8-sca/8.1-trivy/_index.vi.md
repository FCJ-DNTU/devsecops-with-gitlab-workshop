+++
title = "Triển khai SCA với Trivy"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>8.1 </b>"
+++


### 1. Triển Khai thủ công với docker

Bạn có thể tham khảo cái chạy trivy với docker tại đây: [aquasecurity/trivy (github.com)](https://github.com/aquasecurity/trivy)

Tại Build Instance, chúng ta chuyển qua quyền gitlab-runner và vào dự án backend 

```bash
su gitlab-runner
cd
cd builds/...
```

![image.png](/images/8-sca/image.png?featherlight=false&width=60pc)

Tại backend chúng ta tiền hành chạy trivy bằng câu lệnh sau 

```bash
 docker run aquasec/trivy fs .
```

Chúng ta thấy những kết quả được quét in ra trên CLI

![image.png](/images/8-sca/image%201.png?featherlight=false&width=60pc)

### 2 Triển khai với frontend

Chúng ta tạo một branch mới là pipeline-be-8.1-trivy từ nhánh main

![image.png](/images/8-sca/image%202.png?featherlight=false&width=60pc)

Chúng ta vào pipeline editor nhánh mới vừa tạo và tiến hành chỉnh sửa

![image.png](/images/8-sca/image%203.png?featherlight=false&width=60pc)

Nhập dòng lệnh sau đây

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"
    IMAGE_VERSION: "${CI_REGISTRY_USER}/${CI_PROJECT_NAME}-${USER_PROJECT}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}"
    TRIVYFS_REPORT: "trivyfs_scan${CI_PROJECT_NAME}_${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}_report"


{{% notice info %}}
Mình đã lược bớt những stage không liên quan để pipeline nhanh chóng hơn. Sau này chúng ta sẽ tổng hợp lại các stage thành một file lớn để mọi người có thể tham khảo
{{% /notice %}}

Chúng ta tiến hành tạo tags từ nhánh mới tạo để kích hoạt pipeline

![image.png](/images/8-sca/image%204.png?featherlight=false&width=60pc)

Kết quả chạy stage SCA đã thành công, chúng ta vào browse để mở file html lên 

![image.png](/images/8-sca/image%205.png?featherlight=false&width=60pc)

Nhấp vào file đã được tạo

![image.png](/images/8-sca/image%206.png?featherlight=false&width=60pc)

Và đây là file report của trivy, chúng ta thấy những lỗi của các thư viện và các link tham khảo để khắc phục

![image.png](/images/8-sca/image%207.png?featherlight=false&width=60pc)

### 3. Triển khai với Backend

Tạo nhánh mới từ nhánh main

![image.png](/images/8-sca/image%208.png?featherlight=false&width=60pc)

Chỉnh sửa file

![image.png](/images/8-sca/image%209.png?featherlight=false&width=60pc)

Nhập dòng lệnh dưới đây và commit 

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"
    IMAGE_VERSION: "${CI_REGISTRY_USER}/${CI_PROJECT_NAME}-${USER_PROJECT}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}"
    TRIVYFS_REPORT: "trivyfs_scan${CI_PROJECT_NAME}_${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}_report"

stages:
    - clone
    - SAST
    - SCA
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

trivyfs check:
    stage: SCA
    variables:
        GIT_STRATEGY: none
    script:
        - docker run --rm -v $PWD:/${CI_PROJECT_NAME} -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy fs /${CI_PROJECT_NAME} --severity HIGH,CRITICAL --format template --template "@contrib/html.tpl" --output /${CI_PROJECT_NAME}/$TRIVYFS_REPORT.html
    tags:
        - group-ecommerce-shell-runner-build
    only:
        - tags
    artifacts:
        expire_in: 1 day
        paths:
        - $TRIVYFS_REPORT.html

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

Tạo tags mới

![image.png](/images/8-sca/image%2010.png?featherlight=false&width=60pc)

Stage SCA đã hoàn thành

![image.png](/images/8-sca/image%2011.png?featherlight=false&width=60pc)

Và kết quả cuối cùng

![image.png](/images/8-sca/image%2012.png?featherlight=false&width=60pc)