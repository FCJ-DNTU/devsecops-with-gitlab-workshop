+++
title = "CodeClimate (CLI)"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>7.1 </b>"
+++

{{% notice note %}}
🡇 Nội dung 🡇
{{% /notice %}}

Kể từ mục 7.1 trở đi thì chúng ta sẽ thay đổi hostname trong `/etc/hostname` ở deploy instance và build instance thành build-instance và deploy-instance để tránh sự nhầm lẫn giữa hai instance này trong quá trình thực hành!

![image.png](/images/7-sast/a6b40a1e-3e4e-45c6-94d0-2784012df8a3.png?featherlight=false&width=30pc)

![image.png](/images/7-sast/db73149b-65ee-49af-a4f4-c5baaaf5fc6d.png?featherlight=false&width=30pc)

{{% notice note %}}
🡅 Nội dung 🡅
{{% /notice %}}

Trong bài này chúng ta sẽ sử dụng các công cụ SAST (Static Analyze Security Testing) để tích hợp vào trong quy trình CI/CD để phát hiện lỗ hổng bảo mật trong code của bạn.

Bạn có thể lựa chọn các công cụ sao chọn phù hợp với doanh nghiệp của bạn. Xem các công tại đây:  
- [Best Static Code Analysis Tools: User Reviews from August 2024 (g2.com)](https://www.g2.com/categories/static-code-analysis).

- [Best Application Security Testing Reviews 2024 | Gartner Peer Insights](https://www.gartner.com/reviews/market/application-security-testing)

Chúng ta sẽ triển khai các công cụ tại Build Instance.

### 1. Triển Khai SAST với CodeClimate (CLI)

#### 1.1 Cài đặt công cụ

Bạn có thể tham khảo cách cài đặt CodeClimate CLI ở đây: [https://github.com/codeclimate/codeclimate](https://github.com/codeclimate/codeclimate)

Chúng ta Pull docker codeclimate về ở Build Instance

```bash
docker pull codeclimate/codeclimate
```

![image.png](/images/7-sast/image.png?featherlight=false&width=60pc)

Sau đó chúng ta thử chạy CodeClimate CLI  trong thư mục build trong /home/gitlab-runner mà ở các mục trước chúng ta đã chạy CI/CD trên Build Instance này. 

```bash
su gitlab-runner
```

Bạn sẽ vào thư mục backend của bạn

![image.png](/images/7-sast/image%201.png?featherlight=false&width=60pc)

Sau đó gõ CodeClimate CLI như sau 

```bash
sudo docker run --interactive --tty --rm --env CODECLIMATE_CODE="$PWD"  --volume "$PWD":/code  --volume /var/run/docker.sock:/var/run/docker.sock  --volume /tmp/cc:/tmp/cc codeclimate/codeclimate analyze -f html > ecommerce-backend.html
```

Giải thích 

1. **`docker run`**: Lệnh này tạo và chạy một container Docker từ image đã chỉ định.
2. **`-interactive --tty` (`it`)**: Hai tham số này được sử dụng để giữ kết nối tương tác và mở một terminal bên trong container, cho phép bạn giao tiếp với nó.
3. **`-rm`**: Tùy chọn này tự động xóa container sau khi nó dừng lại, nhằm tiết kiệm tài nguyên và tránh việc lưu trữ các container không cần thiết.
4. **`-env CODECLIMATE_CODE="$PWD"`**: Đặt biến môi trường `CODECLIMATE_CODE` bên trong container thành giá trị của thư mục hiện tại (`$PWD`).
5. **`-volume "$PWD":/code`**: Tùy chọn này gắn thư mục hiện tại trên máy chủ (giá trị `$PWD`) vào thư mục `/code` bên trong container. Điều này giúp container truy cập vào mã nguồn của bạn để phân tích.
6. **`-volume /var/run/docker.sock:/var/run/docker.sock`**: Gắn file socket của Docker từ máy chủ vào container. Điều này cho phép container sử dụng Docker CLI bên trong container.
7. **`-volume /tmp/cc:/tmp/cc`**: Gắn thư mục tạm `/tmp/cc` từ máy chủ vào container để lưu trữ các file tạm trong quá trình phân tích.
8. **`codeclimate/codeclimate`**: Đây là image Docker mà bạn đang sử dụng để chạy công cụ phân tích CodeClimate.
9. **`analyze -f html`**: Đây là lệnh bên trong container, yêu cầu CodeClimate thực hiện phân tích mã nguồn và xuất kết quả dưới dạng file HTML.
10. **`> ecommerce-backend.html`**: Phần này chuyển hướng đầu ra (output) của lệnh phân tích vào file `ecommerce-backend.html` trên máy chủ.

Sau khi câu lệnh chạy thì sẽ có một file ecommerce-backend.html được sinh ra ở trong folder backend.

![image.png](/images/7-sast/image%202.png?featherlight=false&width=60pc)

Khi mở file lên thì sẽ là 1 trang web tĩnh với các lỗi đã được detech trong code

![image.png](/images/7-sast/image%203.png?featherlight=false&width=60pc)

{{% notice info %}}
🡇 Nội dung 🡇
{{% /notice %}}

Khi chạy mà terminal bị đứng thì có nghĩa là Sever của bạn không đủ bộ nhớ để lưu trữ (no space left on device). Trong trường ợp có nhiều cách để giải quyết:
- Sử dụng câu lệnh  df -h để xem bộ nhớ còn bao nhiêu
- Xóa đi các container và images không dùng đến.
- Nâng cấp EBS được gắn với instance (khuyến khích 30GB)+ snapshot để phòng trường hợp instance bị die. bạn sử dụng câu lệnh

{{% notice info %}}
🡅 Nội dung 🡅
{{% /notice %}}

Nếu bạn muốn xem html thì đầu tiên hãy copy file html qua /home/ubuntu/

```bash
sudo cp ecommerce-backend.html /home/ubuntu/
```

Khi thực hiện lệnh thì ta thấy file html xuất hiện tại /home/ubuntu

![image.png](/images/7-sast/image%204.png?featherlight=false&width=60pc)

Tiến hành nhấp chuột phải → download

![image.png](/images/7-sast/image%205.png?featherlight=false&width=60pc)

Sau khi mở file thì đây là giao diện hiển thị tất cả các lỗi của source code

![image.png](/images/7-sast/image%206.png?featherlight=false&width=60pc)

#### 1.2 Tích hợp vào CI/CD pipeline

Đầu tiên là dự án backend chúng ta tạo một nhánh mới từ nhánh main để sao này tiện xem lại

![image.png](/images/7-sast/image%207.png?featherlight=false&width=60pc)

![image.png](/images/7-sast/image%208.png?featherlight=false&width=60pc)

Edit file 

![image.png](/images/7-sast/image%209.png?featherlight=false&width=60pc)

Nhập dòng sau đây.

Chúng ta tạo thêm một stage là  test_source_code để test sau khi đã build source code xong. Sau khi hoàn thành việc chạy script ở test_source_code thì sẽ có file html. Và nó sẽ lưu thông qua artifact gitlab với thời hạn là 1 tuần xem thêm tại đây: [Job artifacts | GitLab](https://docs.gitlab.com/ee/ci/jobs/job_artifacts.html).
Chúng ta thêm  when: manual ở hai stage push_container và deploy để deploy thủ công bằng cách nhấn nút play.

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

Sau khi commit chúng ta tags để kích hoạt pipeline

![image.png](/images/7-sast/image%2010.png?featherlight=false&width=60pc)

Pipeline đã chạy thành công  

![image.png](/images/7-sast/image%2011.png?featherlight=false&width=60pc)

Chúng ta sẽ kiểm tra xem source code có vấn đề gì không trước khi tiếp tục qua push_container. Bấm vào browse

![image.png](/images/7-sast/image%2012.png?featherlight=false&width=60pc)

Có 1 file html được tạo ra 

![image.png](/images/7-sast/image%2013.png?featherlight=false&width=60pc)

Bấm để đi đến web tĩnh do gitlab cung cấp 

![image.png](/images/7-sast/image%2014.png?featherlight=false&width=60pc)

Đay là giao diện html của codelimate tạo ra

![image.png](/images/7-sast/image%2015.png?featherlight=false&width=60pc)

Khi chúng ta cảm thấy source không có vaasn đề gì thì chúng ta nhấn nút play lần lượt ở push_container và deploy (khi push_container hoàn thành thì nhấn tiếp deploy)

![image.png](/images/7-sast/image%2016.png?featherlight=false&width=60pc)

![image.png](/images/7-sast/image%2017.png?featherlight=false&width=60pc)

Kiểm tra backend vẫn chạy tốt

![image.png](/images/7-sast/image%2018.png?featherlight=false&width=60pc)

Ở frontend chúng ta sẽ triển khai tương tự

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

Kết quả frontend vẫn chạy tốt

![image.png](/images/7-sast/image%2021.png?featherlight=false&width=60pc)