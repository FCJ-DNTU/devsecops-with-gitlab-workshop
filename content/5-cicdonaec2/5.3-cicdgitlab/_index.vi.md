+++
title = "Cấu hình CI/CD với Gitlab"
date = "`r Sys.Date()`" 
weight = 3
chapter = false
pre = "<b>5.3 </b>"
+++


Mục trước chúng ta đã đăng ký một group runner. Ở bài này chúng ta sẽ triển khai một quy trình CI/CD đơn giản.

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image.png?featherlight=false&width=60pc)

### 1. Tạo user

Để thực hiện shell script được bảo mật hơn thì cứ mỗi nhóm dự án chúng ta sẽ tạo ra một user trong Instance có cùng tên và chỉ cấp quyền thực thi folder build với chỉ người dùng đó. Trong trường hợp này chúng ta cần tạo có tên là ecommerce.

 Chúng ta chạy với quyền root và tạo người dùng ecommerce với các thông tin tương ứng

```bash
exit
adduser ecommerce
```

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image1.png?featherlight=false&width=60pc)

### 2. Cấp quyền visudo

Chúng ta sẽ cấp quyền sudo cho hai user gitlab-runner và ecommerce để khi người dùng sử dụng sudo sẽ không phải cần mật khẩu

Gõ visudo

```bash
visudo
```

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image2.png?featherlight=false&width=60pc)

Hãy thêm dòng lệnh sau dưới “root    ALL=(ALL:ALL) ALL”

```bash

gitlab-runner ALL=(ALL) NOPASSWD:ALL
ecommerce ALL=(ALL) NOPASSWD:ALL
```

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image3.png?featherlight=false&width=60pc)

Nhấn Ctrl+X → Y → Enter để lưu lại.

### 3. Cài đặt công cụ

Chúng ta cần cài đặt công cụ net-tools

```bash
apt install net-tools
```

Chúng ta kiểm tra bằng cách dùng câu lệnh

```bash
netstat -tlpun 
```

Chúng ta có thể thấy 2 project đang chạy ở cổng 3000 (frontend) và cổng 5214 (backend)

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image4.png?featherlight=false&width=60pc)

net-tools sẽ kiểm tra liệu có một process của backend hoặc frontend đang chạy hay không về tiến hành kill nó trong quá trình chạy CI/CD. Nếu không kill đi thì ứng dụng sẽ không thể chyaj được với mã nguồn mới!

Trước khi chạy CI/CD chúng ta cần phải kiểm tra liệu với quyền gitlab-runner thì node, dotnet, pm2 đều có đủ hết không bằng cách

```bash
su gitlab-runner
cd
dotnet --version
node -v 
npm -v
pm2 -v 
```

### 4. Tạo CI/CD Pipeline Cho Backend

Trong project backend, chọn build → Pipelines Editor → Configure pipeline

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image5.png?featherlight=false&width=60pc)

Chúng ta sẽ tiến hành add file gitlab-ci.yml vào nhánh main để giả định chạy pipeline CI/CD khi mà Deploy lên production. Bạn có thể tùy chỉnh tùy theo chiến lược của riêng bạn!

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image6.png?featherlight=false&width=60pc)

Trong file gitlab-ci.yml chúng ta sẽ điền như sau:

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"

stages:
    - build
    - deploy
    
before_script:
    - sudo mkdir -p $PATH_PROJECT

build:
    stage: build
    variables:
        GIT_STRATEGY: clone
    script:
        - dotnet restore
    tags:
        - group-ecommerce-shell-runner
    only:
        - tags

deploy:
    stage: deploy
    variables:
        GIT_STRATEGY: none
    script:
        - sudo cp -rf * ${PATH_PROJECT}
        - sudo chown -R ${USER_PROJECT}. ${PATH_PROJECT}
        - pid=$(sudo netstat -tulnp | grep :5214 | awk '{print $7}' | cut -d'/' -f1) || true
        - if [ -n "$pid" ]; then sudo kill -9 $pid; fi
        - sudo su ${USER_PROJECT} -c "cd ${PATH_PROJECT};nohup dotnet run > log_be.txt 2>&1 &"
    tags:
        - group-ecommerce-shell-runner
    only:
        - tags
```

Giải thích về file ở trên:

**Các Biến**

- USER_PROJECT: "ecommerce": Đặt biến USER_PROJECT thành "ecommerce", được sử dụng để đại diện cho tên người dùng hoặc dự án.
- PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}": Đặt biến PATH_PROJECT thành đường dẫn đến thư mục chứa dự án. CI_PROJECT_NAME là biến mặc định trong GitLab CI/CD đại diện cho tên của dự án hiện tại.

**Trước khi chạy các giai đoạn**

- before_script: sudo mkdir -p $PATH_PROJECT ⇒ có nghĩa là tạo thư muc làm việc, nếu đã có thư mục này r việc chạy lại cũng sẽ không sinh ra lỗi.

**Các Giai Đoạn**

1. **build Stage:**
    - **stage: build**: Xác định rằng đây là giai đoạn xây dựng (build) của pipeline.
    - **variables: GIT_STRATEGY: clone**: Chỉ định cách lấy mã nguồn từ Git. clone có nghĩa là mã nguồn sẽ được clone từ repository.
    - **script:**: Các lệnh sẽ được thực thi trong giai đoạn xây dựng.
        - dotnet restore: Khôi phục các gói NuGet cần thiết cho dự án .NET.
    - **tags: group-ecommerce-shell-runner**: Chỉ định runner nào sẽ thực thi bước này. Đây có thể là một nhóm các runner đã được cấu hình.
    - **only: tags**: Giai đoạn này chỉ được chạy khi có thẻ (tag) được đẩy lên repository.
2. **deploy Stage:**
    - **stage: deploy**: Xác định đây là giai đoạn triển khai (deploy) của pipeline.
    - **variables: GIT_STRATEGY: none**: Không cần phải lấy mã nguồn từ Git trong giai đoạn triển khai.
    - **script:**: Các lệnh sẽ được thực thi trong giai đoạn triển khai.
        - sudo cp -rf * ${PATH_PROJECT}: Sao chép tất cả các tệp và thư mục từ thư mục làm việc hiện tại vào ${PATH_PROJECT}.
        - sudo chown -R ${USER_PROJECT}. ${PATH_PROJECT}: Thay đổi quyền sở hữu của tất cả các tệp và thư mục trong ${PATH_PROJECT} cho người dùng ${USER_PROJECT}.
        - pid=$(sudo netstat -tulnp | grep :5214 | awk '{print $7}' | cut -d'/' -f1) || true: Tìm ID của tiến trình đang lắng nghe trên cổng 5214 và lưu vào biến pid. Nếu không tìm thấy, pid sẽ được gán giá trị rỗng (true ngăn lỗi xảy ra).
        - if [ -n "$pid" ]; then sudo kill -9 $pid; fi: Nếu biến pid không rỗng (nghĩa là có một tiến trình đang lắng nghe trên cổng 5214), tiến trình đó sẽ bị tắt cưỡng bức bằng lệnh kill -9.
        - sudo su ${USER_PROJECT} -c "cd ${PATH_PROJECT};nohup dotnet run > log_be.txt 2>&1 &": Đăng nhập với tư cách người dùng ${USER_PROJECT}, chuyển đến thư mục ${PATH_PROJECT}, và chạy ứng dụng .NET trong nền bằng nohup. Đầu ra sẽ được ghi vào tệp log_be.txt.
    - **tags: group-ecommerce-shell-runner**: Chỉ định runner nào sẽ thực thi bước này.
    - **only: tags**: Giai đoạn này chỉ được chạy khi có thẻ (tag) được đẩy lên repository.

Ở tab Visualize, chúng ta 2 thấy 2 stage là build và deploy với 2 jobs là build backend và deploy backend.

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image7.png?featherlight=false&width=60pc)

Thực hiện Commit changes

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image8.png?featherlight=false&width=60pc)

Chúng ta sẽ thấy pipeline sẽ bị fail

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image9.png?featherlight=false&width=60pc)

Nhấp vào view pipeline

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image10.png?featherlight=false&width=60pc)

Bạn thấy ở đây nó ghi là 0 jobs có nghĩa là khi mà nó đọc file gitlab-ci.yml thì không có job nào ược thực hiện đây là vì ở trên mình có một thuộc tính là only tags. Có nghĩa là các job chỉ có thể chạy khi mà bạn tạo tag.

Bây giờ mình chỉ muốn khi commit vào nhánh main sẽ không có pipeline thực hiện trừ khi tạo tags. Chúng ta sẽ vào Settings → CI/CD → Runners → Expand → Disable Instance Runner

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image11.png?featherlight=false&width=60pc)

Chúng ta vào Editor pipeline và thêm 1 dòng để kiểm tra sau đó commit

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image12.png?featherlight=false&width=60pc)

không có gì thêm ngoài pipeline ban đầu 

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image13.png?featherlight=false&width=60pc)

Bây giờ chúng ta sẽ kiểm tra CI/CD bằng cách tạo tags.

Chọn Code → Tags

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image14.png?featherlight=false&width=60pc)

Tạo một Tags mới

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image15.png?featherlight=false&width=60pc)

Điền tên, tạo từ main và nhấn Create Tag

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image16.png?featherlight=false&width=60pc)

Kiểm tra lại pipeline thì thấy có một pipeline mới đnag chạy hãy nhấp và để xem chi tiết

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image17.png?featherlight=false&width=60pc)

Dểd xem chiết từng dòng lệnh hãy bấm vào stage tương ứng

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image18.png?featherlight=false&width=60pc)

Nhấp vào build backend job

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image19.png?featherlight=false&width=60pc)

Nhấp vào **deploy backend job**

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image20.png?featherlight=false&width=60pc)

Thử kiểm tra xem website có chạy bình thường hay không  

Website vẫn chạy ổn định

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image21.png?featherlight=false&width=60pc)

### 5. Tạo CI/CD Pipeline Cho Backend

Vào Pipelines → Pipeline editor → Main → Điền file → Commit Changes

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image22.png?featherlight=false&width=60pc)

gitlab-ci.yml hãy điền dòng bên dưới

```bash
variables:
    USER_PROJECT: "ecommerce"
    PATH_PROJECT: "/home/${USER_PROJECT}/${CI_PROJECT_NAME}"

stages:
    - build
    - deploy
    
before_script:
    - sudo mkdir -p $PATH_PROJECT

build:
    stage: build
    variables:
        GIT_STRATEGY: clone
    script:
        - npm install
    tags:
        - group-ecommerce-shell-runner
    only:
        - tags

deploy:
    stage: deploy
    variables:
        GIT_STRATEGY: none
    script:
        - sudo cp -rf * ${PATH_PROJECT}
        - sudo chown -R ${USER_PROJECT}. ${PATH_PROJECT}
        - pid=$(sudo netstat -tulnp | grep :3000 | awk '{print $7}' | cut -d'/' -f1) || true
        - if [ -n "$pid" ]; then sudo kill -9 $pid; fi
        - sudo su ${USER_PROJECT} -c "cd ${PATH_PROJECT}; pm2 start npm --name '${CI_PROJECT_NAME}' -- run 'start'"
    tags:
        - group-ecommerce-shell-runner
    only:
        - tags
```

Sau đó Tags và xem pipeline chạy thành công

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image23.png?featherlight=false&width=60pc)

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image24.png?featherlight=false&width=60pc)

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image25.png?featherlight=false&width=60pc)

Sau đó kiểm tra backend vẫn chạy bình thường

![image.png](/images/5-cicdonaec2/5.3-cicdgitlab/image26.png?featherlight=false&width=60pc)