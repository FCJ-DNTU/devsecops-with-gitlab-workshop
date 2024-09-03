+++
title = "SonarQube (Self-Host)"
date = "`r Sys.Date()`" 
weight = 3
chapter = false
pre = "<b>7.3 </b>"
+++

We will deploy SonarQube Self-Host on the Build Instance. You can read more [here](https://docs.sonarsource.com/sonarqube/latest/setup-and-upgrade/install-the-server/installing-sonarqube-from-docker/).

First, create a working directory:

```bash
cd && mkdir -p /root/tools/sonar && vi /root/tools/sonar/docker-compose.yml
```

Enter the following content:

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

Run the `docker-compose.yml` file:

```bash
docker-compose -f tools/sonar/docker-compose.yml up -d
```

When you run `docker ps -a`, you might see the `sonar-sonarqube-1` container exited:

![image.png](/images/7-sast/image%2039.png?featherlight=false&width=60pc)

Use `docker logs` to check the error:

![image.png](/images/7-sast/image%2040.png?featherlight=false&width=60pc)

Elasticsearch uses many virtual memory areas to map large data files into memory. If the value of `vm.max_map_count` is too low, Elasticsearch will not be able to map enough memory areas, leading to startup or runtime errors. This is the cause of the "bootstrap checks failed" error.

Fix this by increasing `max_map_count`:

```bash
sysctl vm.max_map_count
sudo sysctl -w vm.max_map_count=262144
```

![image.png](/images/7-sast/image%2041.png?featherlight=false&width=60pc)

Edit the file `/etc/sysctl.conf`:

```bash
sudo nano /etc/sysctl.conf
```

Add this line to the end of the file:

```bash
vm.max_map_count=262144
```

![image.png](/images/7-sast/image%2042.png?featherlight=false&width=60pc)

Restart `docker-compose` in `/root/tools/sonar/`:

```bash
cd /root/tools/sonar/
docker-compose down
docker-compose up -d
```

![image.png](/images/7-sast/image%2043.png?featherlight=false&width=60pc)

The containers should now be running:

![image.png](/images/7-sast/image%2044.png?featherlight=false&width=60pc)

You need to configure port 9000 to access SonarQube:

![image.png](/images/7-sast/image%2045.png?featherlight=false&width=60pc)

Check port 9000 to access the **SonarQube** main interface:

![image.png](/images/7-sast/image%2046.png?featherlight=false&width=60pc)

For more information on connecting, see: [(2) GitLab Integration | Mapping your organization into SonarQube - YouTube](https://www.youtube.com/watch?v=XX0ey4rRvms)

Log in with:

- username: admin
- password: admin

After logging in, update the password:

![image.png](/images/7-sast/image%2047.png?featherlight=false&width=60pc)

This is the main interface of SonarQube:

![image.png](/images/7-sast/image%2048.png?featherlight=false&width=60pc)

- **Configuration name: ecommerce-gitlab-instance**
- **GitLab API URL:** https://gitlab.com/api/v4

![image.png](/images/7-sast/image%2049.png?featherlight=false&width=60pc)

**Personal Access Token**: You will create a new token in Edit Profile → Access Tokens → Add new token

![image.png](/images/7-sast/image%2050.png?featherlight=false&width=60pc)

Copy this token and paste it into **Personal Access Token** in SonarQube, then save the configuration:

![image.png](/images/7-sast/image%2051.png?featherlight=false&width=60pc)

In **GitLab project onboarding**, enter the token:

![image.png](/images/7-sast/image%2052.png?featherlight=false&width=60pc)

Select the frontend and backend projects to import:

![image.png](/images/7-sast/image%2053.png?featherlight=false&width=60pc)

In the **Set up 2 projects for Clean as You Code** section, select Use the global setting:

![image.png](/images/7-sast/image%2054.png?featherlight=false&width=60pc)

We will select the Backend section first:

![image.png](/images/7-sast/image%2055.png?featherlight=false&width=60pc)

Follow the instructions:

![image.png](/images/7-sast/image%2056.png?featherlight=false&width=60pc)

Create a token for the project:

![image.png](/images/7-sast/image%2057.png?featherlight=false&width=60pc)

Click create:

![image.png](/images/7-sast/image%2058.png?featherlight=false&width=30pc)

Then copy the token:

![image.png](/images/7-sast/image%2059.png?featherlight=false&width=30pc)

Go to the backend project to create a new variable:

![image.png](/images/7-sast/image%2060.png?featherlight=false&width=60pc)

Create as instructed in step 1:

![image.png](/images/7-sast/image%2061.png?featherlight=false&width=60pc)

![image.png](/images/7-sast/image%2062.png?featherlight=false&width=60pc)

You will have 2 variables, `SONAR_TOKEN` and `SONAR_HOST_URL`:

![image.png](/images/7-sast/image%2063.png?featherlight=false&width=60pc)

Create a new branch named `pipeline-be-7.1-sonarqube` from the previous `pipeline-be-codelimate`:

![image.png](/images/7-sast/image%2064.png?featherlight=false&width=60pc)

Edit the `gitlab-ci.yaml` file in the newly created branch:

![image.png](/images/7-sast/image%2065.png?featherlight=false&width=60pc)

Enter the following commands and then commit:

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
You can turn on the Visualize and Validate tabs to check if the content entered in `gitlab-ci.yaml` is correct!
{{% /notice %}}

{{% notice info %}}
🡇 Content 🡇
{{% /notice %}}

You need to adjust the Project Key in this line to match yours to ensure correct results. If you prefer, you can add a new variable to avoid confusion.

```bash
 - "dotnet sonarscanner begin /k:\"$PROJECT_BACKEND_ID\" /d:sonar.token=\"$SONAR_TOKEN\" /d:\"sonar.host.url=$SONAR_HOST_URL\""
```

You can get the Project Key from Project Information → Project Key → copy Project Key

![image.png](/images/7-sast/image%2066.png?featherlight=false&width=60pc)

{{% notice info %}}
🡅 Content 🡅
{{% /notice %}}

We create a new tag to trigger the pipeline.

![image.png](/images/7-sast/image%2067.png?featherlight=false&width=60pc)

The pipeline has successfully run. You can click on the job dependencies tab and turn on show dependencies to see the relationship between stages. Here you can see that the clone step runs first to fetch the project, followed by the SAST stage where both codelimate testing and sonarqube testing run in parallel. After that, the build step is manually triggered, followed by dockerhub pushing and finally deploy.

![image.png](/images/7-sast/image%2068.png?featherlight=false&width=60pc)

The SonarQube testing step has successfully completed.

![image.png](/images/7-sast/image%2069.png?featherlight=false&width=60pc)

{{% notice info %}}
If you cannot run the SonarQube testing step, run it manually using the script in the build instance with script permissions.
{{% /notice %}}

Reload the backend project, and you will see the overview interface.

![image.png](/images/7-sast/image%2070.png?featherlight=false&width=60pc)

Existing issues

![image.png](/images/7-sast/image%2071.png?featherlight=false&width=60pc)

Security issues listed in detail here

![image.png](/images/7-sast/image%2072.png?featherlight=false&width=60pc)

Project metrics

![image.png](/images/7-sast/image%2073.png?featherlight=false&width=60pc)

And code errors

![image.png](/images/7-sast/image%2074.png?featherlight=false&width=60pc)

We will also deploy similarly for frontend.

First, we need to install sonar-scanner in the build instance with `gitlab-runner` permissions.

```bash
# Download and unzip sonar-scanner
wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.8.0.2856-linux.zip
unzip sonar-scanner-cli-4.8.0.2856-linux.zip
# Move sonar-scanner to /usr/local for easier access
sudo mv sonar-scanner-4.8.0.2856-linux /usr/local/sonar-scanner
export PATH=$PATH:/usr/local/sonar-scanner/bin
# Ensure Java is installed on the system
sudo apt-get update
sudo apt-get install --yes openjdk-17-jre
# Check the version
sonar-scanner -v
```

Then, in the `gitlab-ci.yaml` file of the frontend project, enter the following command:


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

I have gone through the SAST implementation with 3 different approaches (CLI, Cloud and Self-Host) with 3 different tools. With each tool, you will find and read the documentation that the tool provides and see if that tool has any approaches and then deploy them into the CI/CD pipeline. With this mindset, you will choose the appropriate approach for your business in each specific case to improve work efficiency and minimize errors in the coding process.