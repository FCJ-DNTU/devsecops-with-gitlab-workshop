+++
title = "Cài đặt Gitlab Runner"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>5.1 </b>"
+++

Bài này chúng sẽ cài đặt gitlab runner để triển khai quy trình CI/CD ở các bài tiếp theo!

Bạn có tham khảo link cài đặt sau: [Install GitLab Runner | GitLab](https://docs.gitlab.com/runner/install/)

Chúng ta hãy tạo một file setup.sh trong /root/tools/gitlab

```bash
cd /root/tools && vi gitlab/setup.sh
```

Nhập các câu lệnh cài đặt 

```bash
#!/bin/bash
apt update -y
curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
apt install gitlab-runner
gitlab-runner --version
```

Chạy câu lệnh

```bash
sh gitlab/setup.sh
```

Cài đặt thành công 

![image.png](/images/5-cicdonaec2/5.1-installgitlabrunner/image.png?featherlight=false&width=60pc)