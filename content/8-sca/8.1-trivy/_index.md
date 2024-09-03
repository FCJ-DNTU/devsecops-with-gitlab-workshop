+++
title = "Implementing SCA with Trivy"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>8.1 </b>"
+++

### 1. Manual Deployment with Docker

You can refer to running Trivy with Docker here: [aquasecurity/trivy (github.com)](https://github.com/aquasecurity/trivy)

On the Build Instance, switch to the gitlab-runner user and go to the backend project:

```bash
su gitlab-runner
cd
cd builds/...
```

![image.png](/images/8-sca/image.png?featherlight=false&width=60pc)

In the backend, we run Trivy with the following command:

```bash
 docker run aquasec/trivy fs .
```

You will see the results of the scan printed on the CLI:

![image.png](/images/8-sca/image%201.png?featherlight=false&width=60pc)

### 2. Deployment with Frontend

Create a new branch called `pipeline-be-8.1-trivy` from the main branch:

![image.png](/images/8-sca/image%202.png?featherlight=false&width=60pc)

Go to the pipeline editor of the newly created branch and make edits:

![image.png](/images/8-sca/image%203.png?featherlight=false&width=60pc)

Enter the following command:

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"
    IMAGE_VERSION: "${CI_REGISTRY_USER}/${CI_PROJECT_NAME}-${USER_PROJECT}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}"
    TRIVYFS_REPORT: "trivyfs_scan${CI_PROJECT_NAME}_${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}_report"


{{% notice info %}}
I have omitted unrelated stages to make the pipeline faster. Later, I will consolidate all stages into one large file for reference.
{{% /notice %}}

Create tags from the newly created branch to trigger the pipeline:

![image.png](/images/8-sca/image%204.png?featherlight=false&width=60pc)

The SCA stage has successfully completed. Go to the browse section to open the HTML file:

![image.png](/images/8-sca/image%205.png?featherlight=false&width=60pc)

Click on the created file:

![image.png](/images/8-sca/image%206.png?featherlight=false&width=60pc)

Here is the Trivy report file. You will see the library issues and reference links for fixes:

![image.png](/images/8-sca/image%207.png?featherlight=false&width=60pc)

### 3. Deployment with Backend

Create a new branch from the main branch:

![image.png](/images/8-sca/image%208.png?featherlight=false&width=60pc)

Edit the file:

![image.png](/images/8-sca/image%209.png?featherlight=false&width=60pc)

Enter the following command and commit:

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

Create new tags:

![image.png](/images/8-sca/image%2010.png?featherlight=false&width=60pc)

The SCA stage has completed:

![image.png](/images/8-sca/image%2011.png?featherlight=false&width=60pc)

And the final result:

![image.png](/images/8-sca/image%2012.png?featherlight=false&width=60pc)
