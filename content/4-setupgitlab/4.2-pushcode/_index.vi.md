+++
title = "Tạo dự án và đẩy mã nguồn lên Gitlab"
date = "`r Sys.Date()`" 
weight = 2
chapter = false
pre = "<b>4.2 </b>"
+++


### 1. Tạo project

Chúng ta đã có 1 group tên là `ecommerce`  và một project có tên là `frontend` từ mục trước. 

Nếu mà bạn chưa tạo thì hãy tiến hành tạo mới group `ecommerce`  và trong group đó tạo mới một project `frontend`

Trong group `ecommerce` chọn Create new project

![image.png](/images/4-setupgitlab/4.2-pushcode/image.png?featherlight=false&width=60pc)

Trong Create new project chọn  Create blank project

![image.png](/images/4-setupgitlab/4.2-pushcode/image1.png?featherlight=false&width=60pc)

Điền các thông tin và nhấn Create Project

![image.png](/images/4-setupgitlab/4.2-pushcode/image2.png?featherlight=false&width=60pc)

Chúng ta đã có project frontend

![image.png](/images/4-setupgitlab/4.2-pushcode/image3.png?featherlight=false&width=60pc)

Chúng ta tiếp tục tạo mới một project có tên là `backend` trong group `ecommerce`  tương tự các bước như trên.

![image.png](/images/4-setupgitlab/4.2-pushcode/image4.png?featherlight=false&width=60pc)

### 2. Đẩy mã nguồn lên Gitlab

Đầu tiên chúng ta sẽ check liệu rằng đã có git trong máy chúng ta hay chưa.

```bash
git --version
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image5.png?featherlight=false&width=60pc)

Chúng ta tiến hành tạo thư mục projects để làm việc

```bash
 mkdir /root/projects && cd /root/projects
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image6.png?featherlight=false&width=60pc)

Chúng ta tiến hành git clone theo như trong gitlab console hướng dẫn

![image.png](/images/4-setupgitlab/4.2-pushcode/image7.png?featherlight=false&width=60pc)

Sau đó chúng ta tạo ra một thư mục mới và cd và thư mục đó:

```bash
mkdir -p ecommerce/ && cd ecommerce/
```

Trên gitlab khi vào project `frontend`  thì chúng sẽ thấy cách setup project. 

![image.png](/images/4-setupgitlab/4.2-pushcode/image27.png?featherlight=false&width=60pc)

Chúng ta sẽ config global giống vậy.

![image.png](/images/4-setupgitlab/4.2-pushcode/image8.png?featherlight=false&width=60pc)

Sau đó chúng ta git clone project frontend của chúng ta về. khi clone thì chúng ta cần phải có email và password .

![image.png](/images/4-setupgitlab/4.2-pushcode/image9.png?featherlight=false&width=60pc)

{{% notice info %}}
Nếu bạn đăng nhập sử dụng gg account hay github account, thì bạn nên set password.
![image.png](/images/4-setupgitlab/4.2-pushcode/image10.png?featherlight=false&width=60pc)
{{% /notice %}}

Sau khi clone xong thì bạn sẽ thực hiện copy tất cả các file trong folder `react-ecommerce-template` qua folder `frontend` mới vừa git clone về

```bash
cd .. 
unzip /home/ubuntu/ecommerce-fullstack-netcore-react.zip 
cp -rf ecommerce-fullstack-netcore-react/frontend/* frontend/
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image11.png?featherlight=false&width=60pc)

Trong `/projects/frontend` ta sẽ tiến hành push code lên gitlab như sau: 

```bash
git checkout -b main
git add .
git commit -m "config(project): Initial frontend project"
git push -f origin main
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image12.png?featherlight=false&width=60pc)

Sau khi push code thì trong project `frontend` đã có các file.

![image.png](/images/4-setupgitlab/4.2-pushcode/image13.png?featherlight=false&width=60pc)

Đối với backend bạn cũng làm tương tự

```bash
cd /root/projects
git clone your_backend_project
cp -rf ecommerce-fullstack-netcore-react/backend/* backend/
cd backend
git checkout -b main
git add .
git commit -m "config(project): Initial backend project"
git push -f origin main
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image14.png?featherlight=false&width=60pc)

Và kết quả đã có mã nguồn trong dự án backend

![image.png](/images/4-setupgitlab/4.2-pushcode/image15.png?featherlight=false&width=60pc)


{{% notice info %}}
🡇 Nội dung 🡇
{{% /notice %}}

Mỗi lần clone về chúng ta đều cần phải đăng nhập khi mà push code và clone code nên rất tốn thời gian và cũng không đảm bảo an toàn nên là chúng ta có thể thiết lập một ssh key cho project:

Tạo mới một ssh key

```
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Bấm Enter tất cả để tạo mặc định, hoặc bạn có thể điền đầy đủ thông tin trong đó

![image.png](/images/4-setupgitlab/4.2-pushcode/image16.png?featherlight=false&width=60pc)

Khởi động SSH agent

```bash
eval $(ssh-agent -s)
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image17.png?featherlight=false&width=60pc)

Thêm SSH key vào SSH agent

```bash
ssh-add ~/.ssh/id_rsa
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image18.png?featherlight=false&width=60pc)

Kiểm tra ssh-add 

```bash
ssh-add -l
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image19.png?featherlight=false&width=60pc)

Copy dòng được cat ra sau đây

```bash
cat ~/.ssh/id_rsa.pub
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image20.png?featherlight=false&width=60pc)

Vào User Setting thêm mới 1 SSH Key

![image.png](/images/4-setupgitlab/4.2-pushcode/image21.png?featherlight=false&width=60pc)

Kiểm tra thử backend bằng cách tạo file **README.md**

![image.png](/images/4-setupgitlab/4.2-pushcode/image22.png?featherlight=false&width=60pc)

Thêm dòng mới

![image.png](/images/4-setupgitlab/4.2-pushcode/image23.png?featherlight=false&width=60pc)

Bạn nên đổi remote HTTPS thành remote SSH. Bạn có thể lấy remote SSH trong project

![image.png](/images/4-setupgitlab/4.2-pushcode/image24.png?featherlight=false&width=60pc)

Tiến hành đổi remote 

```bash
git remote set-url origin git@gitlab.com:ecommerce2100134/backend.git
```

Tiến hành push code

```bash
git add .
git commit -m "update README.md file"
git push -f origin main
```

Và kết quả chúng ta không cần mật khẩu để push code

![image.png](/images/4-setupgitlab/4.2-pushcode/image25.png?featherlight=false&width=60pc)

Cuối cùng File README.md đã được cập nhật thành công!

![image.png](/images/4-setupgitlab/4.2-pushcode/image26.png?featherlight=false&width=60pc)

Link tham khảo: [Use SSH keys to communicate with GitLab | GitLab](https://docs.gitlab.com/ee/user/ssh.html)

{{% notice info %}}
🡅 Nội dung 🡅
{{% /notice %}}
