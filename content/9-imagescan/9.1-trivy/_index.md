+++
title = "Implementing Image Scan with Trivy"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>9.1 </b>"
+++

#### 1. Implementation with Frontend

Similar to previous sections, we create a new branch named `pipeline-fe-9.1-trivy` from the `main` branch. Then proceed to the Pipeline Editor in the newly created branch. Enter the following command:

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"
    IMAGE_VERSION: "${CI_REGISTRY_USER}/${CI_PROJECT_NAME}-${USER_PROJECT}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}"
    TRIVY_IMAGE_REPORT: "trivy_scan_image_${CI_PROJECT_NAME}_${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}_report"

stages:
    - clone
    - SAST
    - build
    - push registry
    - image scan
    - deploy

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

trivy scanning image:
    stage: image scan
    variables:
        GIT_STRATEGY: none
    script:
        -  docker run --rm -v $(pwd):/${CI_PROJECT_NAME} -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy clean --all
        -  docker run --rm -v $(pwd):/${CI_PROJECT_NAME} -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image --format template --template "@contrib/html.tpl" --output /${CI_PROJECT_NAME}/${TRIVY_IMAGE_REPORT}.html ${IMAGE_VERSION}
    tags:
        - group-ecommerce-shell-runner-build
    allow_failure: true
    needs:
        - job: build
    only:
        - tags
    artifacts:
        paths:
        - ${TRIVY_IMAGE_REPORT}.html
        expire_in: 1 day

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
    when: manual
    only:
        - tags
```

We create a new tag and check the results in the pipeline.

![image.png](/images/9-imagescan/image.png?featherlight=false&width=60pc)

Click on browse to view the HTML file, and the Docker file does not have any security issues.

![image.png](/images/9-imagescan/image%201.png?featherlight=false&width=60pc)

#### 2. Implementation with Backend

We create a new branch named `pipeline-be-9.1-trivy` from the `main` branch. Then proceed to the Pipeline Editor in the newly created branch. Enter the following command:

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"
    IMAGE_VERSION: "${CI_REGISTRY_USER}/${CI_PROJECT_NAME}-${USER_PROJECT}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}"
    TRIVY_IMAGE_REPORT: "trivy_scan_image_${CI_PROJECT_NAME}_${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}_report"

stages:
    - clone
    - SAST
    - build
    - push registry
    - image scan
    - deploy

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

trivy scanning image:
    stage: image scan
    variables:
        GIT_STRATEGY: none
    script:
        -  docker run --rm -v $(pwd):/${CI_PROJECT_NAME} -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy clean --all
        -  docker run --rm -v $(pwd):/${CI_PROJECT_NAME} -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image --format template --template "@contrib/html.tpl" --output /${CI_PROJECT_NAME}/${TRIVY_IMAGE_REPORT}.html ${IMAGE_VERSION}
    tags:
        - group-ecommerce-shell-runner-build
    allow_failure: true
    needs:
        - job: build
    only:
        - tags
    artifacts:
        paths:
        - ${TRIVY_IMAGE_REPORT}.html
        expire_in: 1 day

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
    when: manual
    only:
        - tags
```

We create a new tag and check the results in the pipeline.

![image.png](/images/9-imagescan/image%202.png?featherlight=false&width=60pc)

Click on browse to view the HTML file, which displays security issues and remediation suggestions.

![image.png](/images/9-imagescan/image%203.png?featherlight=false&width=60pc)
