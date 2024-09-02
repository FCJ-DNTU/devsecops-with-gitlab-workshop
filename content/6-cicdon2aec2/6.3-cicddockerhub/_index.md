+++
title = "Deploying with Dockerhub Registry"
date = "`r Sys.Date()`" 
weight = 3
chapter = false
pre = "<b>6.3 </b>"
+++

Below is the model we will deploy

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image.png?featherlight=false&width=60pc)

**Build Stage**:

- When the source code is pushed to GitLab, GitLab will trigger the Build Stage. The Build Server will fetch the source code from GitLab and build a Docker image for the application. After building, this Docker image will be pushed to Docker Hub, an online Docker image repository.

**Deploy Stage**:

- In the Deploy Stage, GitLab will trigger an event. The Deploy Server, upon receiving the event, will pull the Docker image from Docker Hub.
- Finally, the deployment server will use the pulled Docker image to run a container. This container is the deployed application and can be accessed from the Internet.

### 1. Setting up Docker Hub

Go to [Docker Hub Container Image Library | App Containerization](https://hub.docker.com/) and create a new account.

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image1.png?featherlight=false&width=60pc)

After creating an account, you will see the main interface

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image2.png?featherlight=false&width=60pc)

If you logged in using Google or GitHub and haven’t created a password yet, go to My profile

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image3.png?featherlight=false&width=60pc)

Select Edit profile

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image4.png?featherlight=false&width=60pc)

And choose reset password, you will receive an email to change your password, click the link to change your password

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image5.png?featherlight=false&width=60pc)

After obtaining a password, log in to Docker on the Build Instance using your username and password

```bash
docker login
```

Your password will be stored at /home/gitlab-runner/.docker/config.json

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image6.png?featherlight=false&width=60pc)

We have successfully logged in on the Build Instance. Now let's move to the Deploy Instance

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image7.png?featherlight=false&width=60pc)

Logged in successfully!

Previously in /root/ecommerce-fullstack-netcore-react, we had the frontend and backend projects

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image8.png?featherlight=false&width=60pc)

We will test the Dockerfile to start both the frontend and backend projects

#### 1.1 Running the backend project with Docker

First, we will write the Dockerfile in the backend project

```bash
cd /root/projects/backend && vi Dockerfile
```

Enter the following content

```
FROM mcr.microsoft.com/dotnet/sdk:6.0
WORKDIR /app

COPY . .
RUN dotnet restore

CMD ["dotnet", "run", "--urls", "http://0.0.0.0:5214"]
```

Before running the Docker file, check if port 5214 is running.

```bash
netstat -tlpun
```

We see that port 5214 is running with PID 70345 

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image9.png?featherlight=false&width=60pc)

Stop the process by running

```bash
kill -9 70345
```

{{% notice info %}}
Please replace 70345 with your backend’s PID
{{% /notice %}}

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image10.png?featherlight=false&width=60pc)

We run the backend container project using the following command

Docker will build the Dockerfile into a Docker image with the tag ecommerce-backend:v1

```bash
docker build -t ecommerce-backend:v1 .
```

We have successfully built the Docker image 

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image11.png?featherlight=false&width=60pc)

Next, we will run Docker with the name backend, with the option to run in the background with internal and external ports set to 5214 from the ecommerce-backend:v1 image 

```bash
docker run --name backend -dp 5214:5214 ecommerce-backend:v1
```

Successfully ran

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image12.png?featherlight=false&width=60pc)

We can also use logs to check what exactly is running in the backend

```bash
docker logs -f id_container
```

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image13.png?featherlight=false&width=60pc)

#### 1.2 Running the frontend project with Docker

First, we will write the Dockerfile in the frontend project

```bash
cd /root/projects/frontend && vi Dockerfile
```

Enter the following content

```
## build ##
FROM node:18-alpine as build

WORKDIR /app
COPY . .
RUN npm install
RUN npm run build

## run ##
FROM nginx:alpine

COPY --from=build /app/build/ /usr/share/nginx/html

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

Similar to the backend, before running the Docker file, check if port 3000 is running.

```bash
netstat -tlpun
```

We see that port 3000 is running with PID 263764

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image14.png?featherlight=false&width=60pc)

Stop the process by stopping pm2 because we had run CI/CD in previous lessons and started pm2. If you use kill -9 PID as usual, pm2 will still restart the project unless you use the following command.

```bash
pm2 stop all
```

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image15.png?featherlight=false&width=60pc)

We run the backend container project using the following command

Docker will build the Dockerfile into a Docker image with the tag ecommerce-frontend:v1

```bash
docker build -t ecommerce-frontend:v1 .
```

We have successfully built the Docker image 

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image16.png?featherlight=false&width=60pc)

Next, we will run Docker with the name frontend with the option to run in the background with internal and external ports set to 3000 from the ecommerce-frontend:v1 image 

```bash
docker run --name frontend -dp 3000:80 ecommerce-frontend:v1
```
The execution was successful

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image17.png?featherlight=false&width=60pc)

### 2. Manual deployment with Dockerhub

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image45.png?featherlight=false&width=60pc)

To save time, I will simulate it on the deploy Instance because it already has the source code. To push an image to the registry, you will push the image in the following format:

```bash
domain/project/repo:tag
```

But since we are pushing to Docker Hub, the format will be:

```bash
user_name/repo:tag
```

Let's tag the frontend image from an existing image first.

Enter the following command:

```bash
docker tag ecommerce-frontend:v1 tunhatphuong/ecommerce-frontend:v1
```

Image created successfully

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image18.png?featherlight=false&width=60pc)

Let's push this image to Docker Hub:

```bash
docker push tunhatphuong/ecommerce-frontend:v1
```

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image19.png?featherlight=false&width=60pc)

In the Docker Hub repository, we can see a new repo that we just pushed.

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image20.png?featherlight=false&width=60pc)

We will stop the frontend container to test pulling the image from Docker Hub:

```bash
docker ps
docker stop container_id
docker rm container_id
docker images
docker rmi your_name/ecommerce-frontend:v1
docker rmi ecommerce-frontend:v1
```

We have removed all containers and images of the frontend.

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image21.png?featherlight=false&width=60pc)

Let's start the frontend project, even though there is no image.

```bash
docker run --name frontend -dp 3000:80 tunhatphuong/ecommerce-frontend:v1
```

When Docker cannot find the image locally, it will pull it from Docker Hub (assuming you are logged in with `docker login`).

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image22.png?featherlight=false&width=60pc)

### 3. CI/CD deployment on 2 Instances with Docker Hub

Before proceeding, we will grant permissions to the gitlab-runner on the Build Instance:

```bash
visudo
```

Add a new line:

```bash
gitlab-runner ALL=(ALL) NOPASSWD:ALL
```

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image23.png?featherlight=false&width=60pc)

#### 3.1 Deployment with the frontend project

Go to the frontend project → Build → Pipelines Editor

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image24.png?featherlight=false&width=60pc)

Access the editor in the main branch.

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image25.png?featherlight=false&width=60pc)

Enter the following lines:

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"
    IMAGE_VERSION: "${CI_REGISTRY_USER}/${CI_PROJECT_NAME}-${USER_PROJECT}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}"

stages:
    - build
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
    only:
        - tags
```

**Variables**

- **USER_PROJECT**: Set to `"ecommerce"`. This is a custom variable to reference the project name.
- **PATH_PROJECT**: This variable combines `USER_PROJECT` with the project name from GitLab CI variables to create a directory path for the project.
- **IMAGE_VERSION**: Tags the Docker image in the format `USER/PROJECT-USER:COMMIT_REF_NAME_COMMIT_SHORT_SHA`. Example: tunhatphuong/frontend-ecommerce:frontend_v1.0.1_abcd123.

**Stages**

1. **build**: This stage builds the Docker image.
2. **push_container**: This stage pushes the Docker image to the registry.
3. **deploy**: This stage deploys the Docker container.

**Jobs**

**`build`**

- **Stage**: `build`
- **Variables**:
    - `GIT_STRATEGY: clone` ensures the repository will be fully cloned for each build.
- **before_script**:
    - Logs into the Docker registry using the provided username and password.
- **script**:
    - Builds the Docker image with the `IMAGE_VERSION` tag.
- **after_script**:
    - Logs out from the Docker registry.
- **tags**:
    - Uses the `group-ecommerce-shell-runner-build` runner to execute this job.
- **only**:
    - This job runs only for tags.

**`push_container`**

- **Stage**: `push_container`
- **Variables**:
    - `GIT_STRATEGY: none` does not require cloning the repository for this stage.
- **before_script**:
    - Logs into the Docker registry.
- **script**:
    - Pushes the Docker image with the `IMAGE_VERSION` tag to the Docker registry.
- **after_script**:
    - Logs out from the Docker registry.
- **tags**:
    - Uses the `group-ecommerce-shell-runner-build`.
- **only**:
    - This job runs only for tags.

**`deploy`**

- **Stage**: `deploy`
- **Variables**:
    - `GIT_STRATEGY: none` does not require cloning the repository for this stage.
- **before_script**:
    - Logs into the Docker registry.
- **script**:
    - Pulls the Docker image with the `IMAGE_VERSION` tag from the registry.
    - Checks if a container with the name `CI_PROJECT_NAME` already exists. If so, it removes that container.
    - Runs a new container with the name `CI_PROJECT_NAME`, mapping port `FRONTEND_PORT` to port 80 of the container, and using the Docker image just pulled.
- **after_script**:
    - Logs out from the Docker registry.
- **tags**:
    - Uses the `group-ecommerce-shell-runner`.
- **only**:
    - This job runs only for tags.

{{% notice info %}}
🡇 Content 🡇
{{% /notice %}}

System Variables:

- **`CI_PROJECT_NAME`**: The name of the GitLab project.
- **`CI_COMMIT_REF_NAME`**: The name of the current branch or tag.
- **`CI_COMMIT_SHORT_SHA`**: The short hash of the current commit.

Custom Variables:

- **`CI_REGISTRY_USER`**: The username or organization in Docker registry.
- **`CI_REGISTRY_PWD`**: The password for Docker registry (this is also a system variable but might need configuration through GitLab).
- `FRONTEND_PORT` : The port for running the frontend.

{{% notice info %}}
🡅 Content 🡅
{{% /notice %}}

Perform commit changes

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image26.png?featherlight=false&width=60pc)

With the custom variables, we will add them in the ecommerce group.

Navigate to group ecommerce → Settings → CI/CD 

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image27.png?featherlight=false&width=60pc)

Select Expand → Add Variable

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image28.png?featherlight=false&width=60pc)

Add the following variables:

- **`CI_REGISTRY_USER`**: Docker Hub username
- **`CI_REGISTRY_PWD`**: Docker Hub password
- `FRONTEND_PORT` : 3000

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image29.png?featherlight=false&width=60pc)

Final result

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image30.png?featherlight=false&width=60pc)

Add Dockerfile to the main branch

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image31.png?featherlight=false&width=60pc)

Add Dockerfile and commit

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image32.png?featherlight=false&width=60pc)

Create Tags in frontend

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image33.png?featherlight=false&width=60pc)

Check the results, all jobs passed

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image34.png?featherlight=false&width=60pc)

Check Docker Hub, a repo **tunhatphuong/frontend-ecommerce** with **tag ecommerce-fe-v1.0.2_dd6e2d25** has been pushed from the Build Instance

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image35.png?featherlight=false&width=60pc)

At the Deploy Instance, when checking, there is a running container with the name **frontend** and image **tunhatphuong/frontend-ecommerce:ecommerce-fe-v1.0.2_dd6e2d25**

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image36.png?featherlight=false&width=60pc)

{{% notice info %}}
Don't worry about the second pipeline run; even though port 3000 is already in use, the script checks, finds, and removes a container named `frontend` if it is running!
{{% /notice %}}

#### 3.2 Deployment with Backend Project

Navigate to the backend project → Build → Pipelines Editor

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image37.png?featherlight=false&width=60pc)

Go to the editor in the main branch

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image38.png?featherlight=false&width=60pc)

Enter the following:

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"
    IMAGE_VERSION: "${CI_REGISTRY_USER}/${CI_PROJECT_NAME}-${USER_PROJECT}:${CI_COMMIT_REF_NAME}_${CI_COMMIT_SHORT_SHA}"

stages:
    - build
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
        - ecommerce-deploy-runner
    only:
        - tags
```

Perform commit changes

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image39.png?featherlight=false&width=60pc)

With the custom variables, add them in the ecommerce group.

Add the variable `BACKEND_PORT` with the value `3000`

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image40.png?featherlight=false&width=60pc)

Add Dockerfile to the main branch and commit

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image41.png?featherlight=false&width=60pc)

Create Tags in backend, check if all jobs passed

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image42.png?featherlight=false&width=60pc)

Check Docker Hub, a repo **tunhatphuong/backend-ecommerce** with **tag ecommerce-be-v1.0.2_934f8c8d** has been pushed from the Build Instance

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image43.png?featherlight=false&width=60pc)

At the Deploy Instance, when checking, there is a running container with the name **backend** and image **tunhatphuong/backend-ecommerce:ecommerce-be-v1.0.2_934f8c8d**

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image44.png?featherlight=false&width=60pc)
