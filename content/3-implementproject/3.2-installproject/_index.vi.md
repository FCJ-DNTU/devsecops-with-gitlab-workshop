+++
title = "Cài đặt mã nguồn"
date = "`r Sys.Date()`" 
weight = 2
chapter = false
pre = "<b>3.2 </b>"
+++

### 1. Tải mã nguồn

Chúng ta sẽ cài đặt mã nguồn từ link sau: [https://drive.google.com/file/d/1sA8_blpoGf6x0DPbcZsdLhuNHru7Xhxa/view?usp=sharing](https://drive.google.com/file/d/1sA8_blpoGf6x0DPbcZsdLhuNHru7Xhxa/view?usp=sharing)

### 2. Điều chỉnh mã nguồn

Sau khi tải về máy của chúng ta, hãy giải nén và thư mục đó.

![image.png](/images/3-implementproject/3.2-installproject/image.png?featherlight=false&width=60pc)

Chúng ta truy cập vào file bên trong và mở terminal 

![image.png](/images/3-implementproject/3.2-installproject/image1.png?featherlight=false&width=60pc)

Chúng ta gõ `code .`  để mở vscode

![image.png](/images/3-implementproject/3.2-installproject/image2.png?featherlight=false&width=60pc)

Chúng ta sẽ chỉnh sửa một chút để project có thể hoạt động đúng.

Chúng ta vào tab search tìm kiếm từ khóa  `your_backend_ipv4`  và thay đổi bằng địa chỉ ipv4 của Deploy Instance 

![image.png](/images/3-implementproject/3.2-installproject/image3.png?featherlight=false&width=60pc)

Tương tự Chúng ta tìm kiếm từ khóa `your_sqlserver_ipv4` và thay đổi bằng địa chỉ ipv4 của Deploy Instance 

![image.png](/images/3-implementproject/3.2-installproject/image4.png?featherlight=false&width=60pc)

Chúng ta sẽ thoát vscode của dự án ecommerce-fullstack-netcore-react, xóa file zip cũ đi và tạo file zip mới

![image.png](/images/3-implementproject/3.2-installproject/image5.png?featherlight=false&width=60pc)

Chúng ta sẽ tiến hành kéo trực tiếp file qua VSCode mà chúng ta SSH đến.

![image.png](/images/3-implementproject/3.2-installproject/image6.png?featherlight=false&width=60pc)

Sau khi kéo vào thì sẽ xuất hiện một file zip với quyền ubuntu

![image.png](/images/3-implementproject/3.2-installproject/image7.png?featherlight=false&width=60pc)

{{% notice info %}}
🡇 Nội dung 🡇
{{% /notice %}}
Ngoài việc kéo file từ bên ngoài vào thì còn một cách khác đó là sử dụng câu lệnh scp:

```bash
scp .\your_file user_name@your_ip:/home/user_name
```
{{% notice info %}}
🡅 Nội dung 🡅
{{% /notice %}}

Sau khi chúng ta có file zip.

Chúng ta sẽ tải unzip về và unzip file ecommerce-fullstack-netcore-react.zip ra thành thư mục và đặt nó tại /root.

```bash
apt install unzip && unzip /home/ubuntu/ecommerce-fullstack-netcore-react.zip /root
```

Khi unzip khi chúng ta sẽ có một thư mục như thế này.

![image.png](/images/3-implementproject/3.2-installproject/image8.png?featherlight=false&width=60pc)