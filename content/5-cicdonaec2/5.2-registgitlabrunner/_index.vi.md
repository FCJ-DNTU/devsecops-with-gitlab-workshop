+++
title = "Đăng ký Runner với Shell Executor"
date = "`r Sys.Date()`" 
weight = 2
chapter = false
pre = "<b>5.2 </b>"
+++

### 1. Các chiến lược triển khai Gitlab runner

#### 1.1 Instance Runner

- Instance Runners được triển khai ở cấp toàn hệ thống và có thể được sử dụng bởi mọi dự án trong GitLab instance đó.
- Thích hợp cho các tổ chức có nhiều dự án cần chia sẻ cùng một tài nguyên runner. Điều này giúp tiết kiệm tài nguyên và dễ quản lý hơn nếu tất cả các dự án đều sử dụng chung một loại runner với cấu hình tương tự.
- Trên GitLab.com, bạn không thể ghi đè thời gian chờ (timeout) cho Instance Runners, vì vậy cần phải sử dụng thời gian chờ được xác định trong dự án.

#### 1.2 Group Runner

- Group Runners được gắn với một nhóm cụ thể và tất cả các dự án trong nhóm đó có thể sử dụng runner này.
- Phù hợp khi có một nhóm các dự án liên quan cần sử dụng chung một runner với các yêu cầu đặc thù cho nhóm đó. Group Runners giúp quản lý tài nguyên hiệu quả hơn giữa các dự án trong cùng một nhóm mà không bị ảnh hưởng bởi các dự án khác trong hệ thống.

#### 3. Project Runner

- Project Runners được gắn trực tiếp với một dự án duy nhất.
- Thích hợp cho các dự án có nhu cầu đặc biệt về môi trường hoặc tài nguyên mà không thể chia sẻ với các dự án khác. Điều này cung cấp sự kiểm soát cao nhất về cách runner hoạt động và cho phép tối ưu hóa riêng cho từng dự án.
- Có thể ghi đè thời gian chờ (timeout) trực tiếp ở cấp độ dự án mà không ảnh hưởng đến các dự án khác.

### 2. Đăng ký Runner với Shell Executor

Chúng ta sẽ triển khai Gitlab Runner trên cùng một Instance nên chúng ta nên chọn theo chiến lược  **Group Runner.**

Group Runner sẽ phục vụ tất cả các dự án trong nhóm **ecommerce**, bao gồm cả **frontend** và **backend**. Điều này giúp chúng ta quản lý và chia sẻ tài nguyên hiệu quả hơn giữa các dự án liên quan trong cùng một nhóm.

Đầu tiên chúng ta vào group **ecommerce → Build → Runners**

![image.png](/images/5-cicdonaec2/5.2-registgitlabrunner/image.png?featherlight=false&width=60pc)

Nhấn vào New group runner để tạo

![image.png](/images/5-cicdonaec2/5.2-registgitlabrunner/image1.png?featherlight=false&width=60pc)
Tạo một runner mới

- **Tags: group-ecommerce-shell-runner**
- Enable: Run untagged jobs
- Nhấn nút Create Runner để tạo

![image.png](/images/5-cicdonaec2/5.2-registgitlabrunner/image2.png?featherlight=false&width=60pc)

Sau khi tạo chúng ta sẽ có các bước để đăng ký runner

![image.png](/images/5-cicdonaec2/5.2-registgitlabrunner/image3.png?featherlight=false&width=60pc)

Đầu tiên chúng ta bắc buộc phải chuyển qua quyền của gitlab-runner để runner có thể chạy đúng

```bash
su gitlab-runner
cd
```

![image.png](/images/5-cicdonaec2/5.2-registgitlabrunner/image4.png?featherlight=false&width=40pc)

Ở bước 1 và bước chúng copy chạy lệnh và điền thông tin như hình sau:

- URL: https://gitlab.com
- Name runner: group-ecommerce-shell-runner-build
- Executor: shell

![image.png](/images/5-cicdonaec2/5.2-registgitlabrunner/image5.png?featherlight=false&width=60pc)

Câu file cấu hình sẽ được lưu tại /home/gitlab-runner/.gitlab-runner/config.toml

```bash
vi /etc/gitlab-runner/config.toml
```

![image.png](/images/5-cicdonaec2/5.2-registgitlabrunner/image6.png?featherlight=false&width=60pc)

Chúng ta tiến hành sửa concurrent = 2 để group runner có thể đồng thời chạy cả backend lẫn frontend và Esc+ :x để lưu lại

![image.png](/images/5-cicdonaec2/5.2-registgitlabrunner/image7.png?featherlight=false&width=60pc)

Bước 3 chúng ta chạy

```bash
gitlab-runner run
```

![image.png](/images/5-cicdonaec2/5.2-registgitlabrunner/image8.png?featherlight=false&width=60pc)

Chúng ta sẽ thấy bên gitlab sẽ xuất hiện giao diện đã kết nối

![image.png](/images/5-cicdonaec2/5.2-registgitlabrunner/image9.png?featherlight=false&width=60pc)


{{% notice info %}}
🡇 Nội dung 🡇
{{% /notice %}}

Việc kết nối đã thành công nhưng đôi khi lệnh này khi thoát ra bằng Ctrl C thì Instance của chúng ta sẽ trong nhận được Trigger từ ci-gitlab.yml nên là cách tốt nhất là hãy cho nó chạy trong nền bằng cách:

```bash
nohup gitlab-runner run > start-gitlab-runner.txt 2>&1 &
```

![image.png](/images/5-cicdonaec2/5.2-registgitlabrunner/image10.png?featherlight=false&width=60pc)

{{% notice info %}}
🡅 Nội dung 🡅
{{% /notice %}}

Vậy là chúng ta đã hoàn tất việc đăng ký!