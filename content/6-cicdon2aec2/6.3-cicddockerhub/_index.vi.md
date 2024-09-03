+++
title = "Triển khai với Dockerhub Registry"
date = "`r Sys.Date()`" 
weight = 3
chapter = false
pre = "<b>6.3 </b>"
+++

Sau đây là mô hình chúng ta sẽ triển khai

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image.png?featherlight=false&width=60pc)

**Build Stage**:

- Khi mã nguồn được đẩy lên GitLab, GitLab sẽ kích hoạt Build Stage. Build Server sẽ lấy mã nguồn từ GitLab, build Docker image cho ứng dụng. Sau khi build xong, Docker image này sẽ được đẩy lên Docker Hub, một kho chứa hình ảnh Docker trực tuyến.

**Deploy Stage**:

- Đến Deploy Stage GitLab sẽ kích hoạt sự kiện. Deploy Server nhận sự kiện sẽ pull  Docker image từ Docker Hub về.
- Cuối cùng, máy chủ triển khai sẽ sử dụng hình ảnh Docker vừa tải về để chạy một container. Container này chính là ứng dụng đã được triển khai và có thể truy cập từ Internet.

### 1. Thiết lập docker hub

 Bạn truy cập [Docker Hub Container Image Library | App Containerization](https://hub.docker.com/) và tạo một tài khoản mới.

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image1.png?featherlight=false&width=60pc)

Sau khi tạo tài khoản thì bạn sẽ vào được giao diện chính

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image2.png?featherlight=false&width=60pc)

Nếu bạn đăng nhập bằng Google hoặc Github mà chưa tạo mật khẩu thì hãy vào My profile

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image3.png?featherlight=false&width=60pc)

Chọn Edit profile

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image4.png?featherlight=false&width=60pc)

Và chọn reset password, bạn sẽ nhận được email thay đổi mật khẩu và hãy bấm vào link được thay đổi mật khẩu của bạn

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image5.png?featherlight=false&width=60pc)

Sau khi có mật khẩu tại Build Instance hãy đăng nhập vào docker điền username và mật khẩu của bạn  

```bash
docker login
```

Mật khẩu của bạn sẽ được lưu tại /home/gitlab-runner/.docker/config.json

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image6.png?featherlight=false&width=60pc)

Chúng ta đã đăng nhập thành công tại Build Instance. Bây giờ chúng ta sẽ chuyển qua Deploy Instance 

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image7.png?featherlight=false&width=60pc)

Đã đăng nhập thành công!

Trước đó trong /root/ecommerce-fullstack-netcore-react chúng ta đã có dự án frontend và backend

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image8.png?featherlight=false&width=60pc)

Chúng ta sẽ chạy thử Dockerfile để khởi động hai dự án frontend và backend 

#### 1.1 Chạy dự án backend bằng docker

 Đầu tiên chúng ta sẽ viết Dockerfile tại dự án backend

```bash
cd /root/projects/backend && vi Dockerfile
```

Nhập nội dung sau đây

```
FROM mcr.microsoft.com/dotnet/sdk:6.0
WORKDIR /app

COPY . .
RUN dotnet restore

CMD ["dotnet", "run", "--urls", "http://0.0.0.0:5214"]
```

Trước chạy file docker thì chúng ta kiểm tra xem liệu port 5214 có đang chạy hay không.

```bash
netstat -tlpun
```

Chúng ta đã thấy được port 5214 đang chạy với PID là 70345 

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image9.png?featherlight=false&width=60pc)

Tiến hành dừng tiến trình bằng cách 

```bash
kill -9 70345
```

{{% notice info %}}
Hãy thay thế 70345 bằng PID của backend bạn
{{% /notice %}}

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image10.png?featherlight=false&width=60pc)

Chúng ta chạy dự án container backend lên bằng câu lệnh sau

Docker sẽ build Dockerfile thành Docker image  với tag ecommerce-backend:v1

```bash
docker build -t ecommerce-backend:v1 .
```

Chúng ta đã build docker image thành công 

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image11.png?featherlight=false&width=60pc)

Tiếp theo chúng ta sẽ chạy docker với tên là backend  với tùy chọn chạy dưới nền với cổng bên trong và bên ngoài là 5214 từ image ecommerce-backend:v1 

```bash
docker run --name backend -dp 5214:5214 ecommerce-backend:v1
```

Kết quả chạy đã thành công

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image12.png?featherlight=false&width=60pc)

Chúng ta cũng có thể logs để kiểm tra chính xác backend đang chạy những gì

```bash
docker logs -f id_container
```

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image13.png?featherlight=false&width=60pc)

#### 1.2 Chạy dự án frontend bằng docker

 Đầu tiên chúng ta sẽ viết Dockerfile tại dự án frontend

```bash
cd /root/projects/frontend && vi Dockerfile
```

Nhập nội dung sau đây

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

Cũng như backend trước chạy file docker thì chúng ta kiểm tra xem liệu port 3000 có đang chạy hay không.

```bash
netstat -tlpun
```

Chúng ta đã thấy được port 3000 đang chạy với PID là 263764

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image14.png?featherlight=false&width=60pc)

Tiến hành dừng tiến trình bằng cách tắt pm2 bởi vì chúng ta đã chạy CI/CD ở các bài trước và đã start pm2. Nếu bạn dùng kill -9 PID như bình thường thì pm2 vẫn restart project trừ khi bạn dùng lệnh dưới đây.

```bash
pm2 stop all
```

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image15.png?featherlight=false&width=60pc)

Chúng ta chạy dự án container backend lên bằng câu lệnh sau

Docker sẽ build Dockerfile thành Docker image  với tag ecommerce-frontend:v1

```bash
docker build -t ecommerce-frontend:v1 .
```

Chúng ta đã build docker image thành công 

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image16.png?featherlight=false&width=60pc)

Tiếp theo chúng ta sẽ chạy docker với tên là frontend với tùy chọn chạy dưới nền với cổng bên trong và bên ngoài là 3000 từ image ecommerce-frontend:v1 

```bash
docker run --name frontend -dp 3000:80 ecommerce-frontend:v1
```

Kết quả chạy đã thành công

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image17.png?featherlight=false&width=60pc)

### 2. Triển khai thủ công trên với Dockerhub

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image45.png?featherlight=false&width=60pc)

Để tiết kiệm thời gian thì chúng ta sẽ tiến hành mô phỏng trên deploy Intance luôn bời vì nó đã có src code để push image lên registry bạn sẽ push image theo định dạng sau:

```bash
domain/project/repo:tag
```

Nhưng do chúng ta đang push lên docker hub thì định đạng chỉ là 

```bash
user_name/repo:tag
```

Chúng ta tiến hành tạo tag trước với frontend từ image cũ đã có trước đó

Chúng ta nhập câu lệnh sau

```bash
docker tag ecommerce-frontend:v1 tunhatphuong/ecommerce-frontend:v1
```

Đã tạo image thành công

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image18.png?featherlight=false&width=60pc)

Chúng ta đẩy image này lên docker hub 

```bash
docker push tunhatphuong/ecommerce-frontend:v1
```

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image19.png?featherlight=false&width=60pc)

Trong repo trên docker hub thì chúng ta thấy 1 repo mới mà chúng ta mới push lên

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image20.png?featherlight=false&width=60pc)

Chúng ta sẽ stop container frontend để thử pull image từ docker hub về

```bash
docker ps
docker stop container_id
docker rm container_id
docker images
docker rmi your_name/ecommerce-frontend:v1
docker rmi ecommerce-frontend:v1
```

Chúng ta xóa hết container, image của frontend

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image21.png?featherlight=false&width=60pc)

Chúng tiến hành khởi chạy dự án frontend mặc dù không có image

```bash
docker run --name frontend -dp 3000:80 tunhatphuong/ecommerce-frontend:v1
```

Khi docker kiếm image trong local không có docker sẽ pull về từ docker hub (trong trường hợp bạn đã đăng nhập bằng docker login)

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image22.png?featherlight=false&width=60pc)

### 3. Triển khai CI/CD trên 2 Instance với Docker Hub

Trước khi thực hiện thì chúng ta sẽ cấp quyền cho gitlab-runner trong Build Instance :

```bash
visudo
```

Thêm dòng mới

```bash
gitlab-runner ALL=(ALL) NOPASSWD:ALL
```

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image23.png?featherlight=false&width=60pc)

#### 3.1  Triển khai với dự án frontend

Chúng ta vào dự án frontend → Build → Pipelines Editor

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image24.png?featherlight=false&width=60pc)

Chúng ta vào editor ở nhánh main

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image25.png?featherlight=false&width=60pc)

Chúng ta nhập dòng sau:

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

**Các Biến**

- **USER_PROJECT**: Được đặt là `"ecommerce"`. Đây là một biến tùy chỉnh để tham chiếu tên dự án.
- **PATH_PROJECT**: Biến này kết hợp `USER_PROJECT` với tên dự án từ các biến CI của GitLab, tạo ra đường dẫn thư mục cho dự án.
- **IMAGE_VERSION**: Đặt thẻ cho Docker image theo định dạng `USER/PROJECT-USER:COMMIT_REF_NAME_COMMIT_SHORT_SHA`. Ví dụ: tunhatphuong/frontend-ecommerce:frontend_v1.0.1_abcd123

**Các Stage**

1. **build**: Giai đoạn này xây dựng Docker image.
2. **push_container**: Giai đoạn này đẩy Docker image lên registry.
3. **deploy**: Giai đoạn này triển khai Docker container.

**Các Job**

**`build`**

- **Stage**: `build`
- **Biến**:
    - `GIT_STRATEGY: clone` đảm bảo rằng repository sẽ được clone mới hoàn toàn cho mỗi lần build.
- **before_script**:
    - Đăng nhập vào Docker registry với thông tin người dùng và mật khẩu.
- **script**:
    - Xây dựng Docker image với thẻ `IMAGE_VERSION`.
- **after_script**:
    - Đăng xuất khỏi Docker registry.
- **tags**:
    - Sử dụng runner `group-ecommerce-shell-runner-build` để thực hiện job này.
- **only**:
    - Chỉ chạy job này cho các tag.

**`push_container`**

- **Stage**: `push_container`
- **Biến**:
    - `GIT_STRATEGY: none` không cần clone repository cho giai đoạn này.
- **before_script**:
    - Đăng nhập vào Docker registry.
- **script**:
    - Đẩy Docker image với thẻ `IMAGE_VERSION` lên Docker registry.
- **after_script**:
    - Đăng xuất khỏi Docker registry.
- **tags**:
    - Sử dụng runner `group-ecommerce-shell-runner-build`.
- **only**:
    - Chỉ chạy job này cho các tag.

**`deploy`**

- **Stage**: `deploy`
- **Biến**:
    - `GIT_STRATEGY: none` không cần clone repository cho giai đoạn này.
- **before_script**:
    - Đăng nhập vào Docker registry.
- **script**:
    - Kéo Docker image với thẻ `IMAGE_VERSION` từ registry.
    - Kiểm tra xem container với tên `CI_PROJECT_NAME` đã tồn tại hay chưa. Nếu có, xóa container đó.
    - Chạy một container mới với tên `CI_PROJECT_NAME`, ánh xạ cổng `FRONTEND_PORT` đến cổng 80 của container, và sử dụng Docker image vừa kéo.
- **after_script**:
    - Đăng xuất khỏi Docker registry.
- **tags**:
    - Sử dụng runner `group-ecommerce-shell-runner`.
- **only**:
    - Chỉ chạy job này cho các tag.

{{% notice info %}}
🡇 Nội dung 🡇
{{% /notice %}}

Biến Hệ Thống:

- **`CI_PROJECT_NAME`**: Tên của dự án GitLab.
- **`CI_COMMIT_REF_NAME`**: Tên của nhánh hoặc tag hiện tại.
- **`CI_COMMIT_SHORT_SHA`**: Hash ngắn của commit hiện tại.

Biến Tùy Chỉnh

- **`CI_REGISTRY_USER`**: Tên người dùng hoặc tổ chức trong Docker registry.
- **`CI_REGISTRY_PWD`**: Mật khẩu của người dùng Docker registry (biến này cũng là hệ thống nhưng có thể cần cấu hình qua GitLab).
- `FRONTEND_PORT` : Cổng để chạy frontend

{{% notice info %}}
🡅 Nội dung 🡅
{{% /notice %}}

Thực hiện commit changes

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image26.png?featherlight=false&width=60pc)

Với các biến tùy chỉnh chúng ta sẽ thêm vào ở group ecommerce

Chúng ta vào group ecommerce → Settings → CI/CD 

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image27.png?featherlight=false&width=60pc)

Chọn Expand → Add Variable

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image28.png?featherlight=false&width=60pc)

Thêm các biến như sau 

- **`CI_REGISTRY_USER`**: Tên người dùng của docker hub
- **`CI_REGISTRY_PWD`**: Mật khẩu docker hub
- `FRONTEND_PORT` : 3000

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image29.png?featherlight=false&width=60pc)

Kết quả cuối cùng

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image30.png?featherlight=false&width=60pc)

Chúng ta Dockerfile vào nhánh main

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image31.png?featherlight=false&width=60pc)

Thực hiện thêm Dockerfile và commit

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image32.png?featherlight=false&width=60pc)

Tạo Tags trong frontend 

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image33.png?featherlight=false&width=60pc)

Kiểm tra kết quả các job đều pass

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image34.png?featherlight=false&width=60pc)

Thử xem trên docker hub, một repo là **tunhatphuong/frontend-ecommerce** với ****tag là ecommerce-fe-v1.0.2_dd6e2d25 đã được push lên từ Build Instance

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image35.png?featherlight=false&width=60pc)

Và tại Deploy Instance khi chúng ta kiểm tra có một container được chạy lên với name là frontend và image là tunhatphuong/frontend-ecommerce:ecommerce-fe-v1.0.2_dd6e2d25

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image36.png?featherlight=false&width=60pc)

{{% notice info %}}
Bạn đừng lo ở lần chạy pipeline thứ hai, mặc dù đã có port 3000 đang chạy, nhưng trong đoạn script đã check, tìm và xóa container tên là frontend nếu nó đang chạy!
{{% /notice %}}

#### 3.2  Triển khai với dự án backend

Chúng ta vào dự án backend→ Build → Pipelines Editor

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image37.png?featherlight=false&width=60pc)

Chúng ta vào editor ở nhánh main

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image38.png?featherlight=false&width=60pc)

Chúng ta nhập dòng sau:

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

Thực hiện commit changes

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image39.png?featherlight=false&width=60pc)

Với các biến tùy chỉnh chúng ta sẽ thêm vào ở group ecommerce

Thêm các biến `BACKEND_PORT`  với giá trị `3000`

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image40.png?featherlight=false&width=60pc)

Chúng ta Dockerfile vào nhánh main thực hiện thêm Dockerfile và commit

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image41.png?featherlight=false&width=60pc)

Tạo Tags trong backend, kiểm tra kết quả các job đều pass

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image42.png?featherlight=false&width=60pc)

Thử xem trên docker hub, một repo là **tunhatphuong/backend-ecommerce** với ****tag là ecommerce-fe-v1.0.2_dd6e2d25 đã được push lên từ Build Instance

![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image43.png?featherlight=false&width=60pc)

Và tại Deploy Instance khi chúng ta kiểm tra có một container được chạy lên với name là backend và image là  tunhatphuong/backend-ecommerce:ecommerce-be-v1.0.2_934f8c8d
![image.png](/images/6-cicdon2aec2/6.3-cicddockerhub/image44.png?featherlight=false&width=60pc)
