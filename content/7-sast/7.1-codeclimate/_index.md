+++
title = "CodeClimate (CLI)"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>7.1 </b>"
+++

{{% notice note %}}
🡇 Content 🡇
{{% /notice %}}

From section 7.1 onwards, we will change the hostname in `/etc/hostname` on the deploy instance and build instance to `build-instance` and `deploy-instance` to avoid confusion between these instances during practice!

![image.png](/images/7-sast/a6b40a1e-3e4e-45c6-94d0-2784012df8a3.png?featherlight=false&width=30pc)

![image.png](/images/7-sast/db73149b-65ee-49af-a4f4-c5baaaf5fc6d.png?featherlight=false&width=30pc)

{{% notice note %}}
🡅 Content 🡅
{{% /notice %}}

In this article, we will use SAST (Static Analyze Security Testing) tools to integrate into the CI/CD process to detect security vulnerabilities in your code.

You can choose tools that are suitable for your business. See the tools here:  
- [Best Static Code Analysis Tools: User Reviews from August 2024 (g2.com)](https://www.g2.com/categories/static-code-analysis).

- [Best Application Security Testing Reviews 2024 | Gartner Peer Insights](https://www.gartner.com/reviews/market/application-security-testing)

We will deploy the tools on the Build Instance.

### 1. Deploying SAST with CodeClimate (CLI)

#### 1.1 Installing the Tool

You can refer to how to install CodeClimate CLI here: [https://github.com/codeclimate/codeclimate](https://github.com/codeclimate/codeclimate)

We pull the CodeClimate Docker image on the Build Instance:

```bash
docker pull codeclimate/codeclimate
```

![image.png](/images/7-sast/image.png?featherlight=false&width=60pc)

Then we try running the CodeClimate CLI in the build directory in `/home/gitlab-runner` where we previously ran CI/CD on this Build Instance.

```bash
su gitlab-runner
```

Navigate to your backend directory:

![image.png](/images/7-sast/image%201.png?featherlight=false&width=60pc)

Then run the CodeClimate CLI as follows:

```bash
sudo docker run --interactive --tty --rm --env CODECLIMATE_CODE="$PWD"  --volume "$PWD":/code  --volume /var/run/docker.sock:/var/run/docker.sock  --volume /tmp/cc:/tmp/cc codeclimate/codeclimate analyze -f html > ecommerce-backend.html
```

Explanation:

1. **`docker run`**: This command creates and runs a Docker container from the specified image.
2. **`-interactive --tty` (`it`)**: These two options keep the interactive connection open and start a terminal inside the container, allowing you to interact with it.
3. **`-rm`**: This option automatically removes the container after it stops, saving resources and avoiding unnecessary storage of unused containers.
4. **`-env CODECLIMATE_CODE="$PWD"`**: Sets the `CODECLIMATE_CODE` environment variable inside the container to the current directory (`$PWD`).
5. **`-volume "$PWD":/code`**: This option mounts the current directory on the host machine (value `$PWD`) into the `/code` directory inside the container, allowing the container to access your source code for analysis.
6. **`-volume /var/run/docker.sock:/var/run/docker.sock`**: Mounts the Docker socket file from the host into the container. This allows the container to use the Docker CLI inside the container.
7. **`-volume /tmp/cc:/tmp/cc`**: Mounts the `/tmp/cc` temporary directory from the host into the container to store temporary files during the analysis process.
8. **`codeclimate/codeclimate`**: This is the Docker image you are using to run the CodeClimate analysis tool.
9. **`analyze -f html`**: This is the command inside the container, instructing CodeClimate to analyze the source code and output the results in HTML format.
10. **`> ecommerce-backend.html`**: This redirects the output of the analysis command to the `ecommerce-backend.html` file on the host.

After the command runs, a `ecommerce-backend.html` file will be generated in the backend folder.

![image.png](/images/7-sast/image%202.png?featherlight=false&width=60pc)

When you open the file, it will be a static webpage displaying the detected issues in the code.

![image.png](/images/7-sast/image%203.png?featherlight=false&width=60pc)

{{% notice info %}}
🡇 Content 🡇
{{% /notice %}}

If the terminal hangs during execution, it means your server does not have enough memory (no space left on device). In this case, there are several ways to resolve it:
- Use the command `df -h` to check the available memory.
- Delete unused containers and images.
- Upgrade the EBS attached to the instance (recommended 30GB) + snapshot to prevent data loss if the instance dies.

{{% notice info %}}
🡅 Content 🡅
{{% /notice %}}

If you want to view the HTML file, first copy it to `/home/ubuntu/`:

```bash
sudo cp ecommerce-backend.html /home/ubuntu/
```

When you execute the command, you will see the HTML file appear in `/home/ubuntu`:

![image.png](/images/7-sast/image%204.png?featherlight=false&width=60pc)

Proceed to right-click → download:

![image.png](/images/7-sast/image%205.png?featherlight=false&width=60pc)

After opening the file, here is the interface displaying all the issues in the source code:

![image.png](/images/7-sast/image%206.png?featherlight=false&width=60pc)

#### 1.2 Integrating into the CI/CD Pipeline

First, in the backend project, create a new branch from the main branch for future reference:

![image.png](/images/7-sast/image%207.png?featherlight=false&width=60pc)

![image.png](/images/7-sast/image%208.png?featherlight=false&width=60pc)

Edit the file:

![image.png](/images/7-sast/image%209.png?featherlight=false&width=60pc)

Enter the following line.

We create a new stage called `test_source_code` to test after the source code has been built. After completing the script in `test_source_code`, an HTML file will be generated, and it will be saved through the GitLab artifact with a retention period of one week. See more here: [Job artifacts | GitLab](https://docs.gitlab.com/ee/ci/jobs/job_artifacts.html).
We add `when: manual` to the `push_container` and `deploy` stages to deploy manually by pressing the play button.

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"
    IMAGE_VERSION: "${CI_REGISTRY_USER}/${CI_PROJECT_NAME}-${USER_PROJECT}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}"
    CODECLIMATE_FILE: "${CI_PROJECT_NAME}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}.html"

stages:
    - build
    - test_source_code
    - push_container
    - deploy

before_script:
    - sudo mkdir -p $PATH_PROJECT

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

test_source_code:
    stage: test_source_code
    variables:
        GIT_STRATEGY: none
    script:
        - sudo docker run --rm --env CODECLIMATE_CODE="$PWD"  --volume "$PWD":/code  --volume /var/run/docker.sock:/var/run/docker.sock  --volume /tmp/cc:/tmp/cc codeclimate/codeclimate analyze -f html > $CODECLIMATE_FILE
    tags:
        - group-ecommerce-shell-runner-build
    only:
        - tags
    artifacts:
        paths:
        - $CODECLIMATE_FILE
        expire_in: 1 week

push_container:
    stage: push_container
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
    when: manual
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
    when: manual
    only:
        - tags
```

After committing, we tag to trigger the pipeline.

![image.png](/images/7-sast/image%2010.png?featherlight=false&width=60pc)

The pipeline ran successfully.

![image.png](/images/7-sast/image%2011.png?featherlight=false&width=60pc)

We will check if there are any issues with the source code before proceeding with `push_container`. Click on `browse`.

![image.png](/images/7-sast/image%2012.png?featherlight=false&width=60pc)

An HTML file has been generated.

![image.png](/images/7-sast/image%2013.png?featherlight=false&width=60pc)

Click to go to the static web page provided by GitLab.

![image.png](/images/7-sast/image%2014.png?featherlight=false&width=60pc)

This is the HTML interface generated by CodeClimate.

![image.png](/images/7-sast/image%2015.png?featherlight=false&width=60pc)

When we are confident that the source code has no issues, we click the play button sequentially for `push_container` and `deploy` (after `push_container` is completed, click `deploy`).

![image.png](/images/7-sast/image%2016.png?featherlight=false&width=60pc)

![image.png](/images/7-sast/image%2017.png?featherlight=false&width=60pc)

Check that the backend is still running well.

![image.png](/images/7-sast/image%2018.png?featherlight=false&width=60pc)

We will deploy the frontend similarly.

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"
    IMAGE_VERSION: "${CI_REGISTRY_USER}/${CI_PROJECT_NAME}-${USER_PROJECT}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}"
    CODECLIMATE_FILE: "${CI_PROJECT_NAME}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}.html"

stages:
    - build
    - test_source_code
    - push_container
    - deploy
    
before_script:
    - sudo mkdir -p $PATH_PROJECT

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

test_source_code:
    stage: test_source_code
    variables:
        GIT_STRATEGY: none
    script:
        - sudo docker run --rm --env CODECLIMATE_CODE="$PWD"  --volume "$PWD":/code  --volume /var/run/docker.sock:/var/run/docker.sock  --volume /tmp/cc:/tmp/cc codeclimate/codeclimate analyze -f html > $CODECLIMATE_FILE
    tags:
        - group-ecommerce-shell-runner-build
    only:
        - tags
    artifacts:
        paths:
        - $CODECLIMATE_FILE
        expire_in: 1 week

push_container:
    stage: push_container
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
    when: manual
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
    when: manual
    only:
        - tags

```

![image.png](/images/7-sast/image%2019.png?featherlight=false&width=60pc)

![image.png](/images/7-sast/image%2020.png?featherlight=false&width=60pc)

The frontend is still running well.

![image.png](/images/7-sast/image%2021.png?featherlight=false&width=60pc)
