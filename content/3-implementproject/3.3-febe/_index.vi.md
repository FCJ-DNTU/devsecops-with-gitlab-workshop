+++
title = "Triển khai dự án Frontend và Backend"
date = "`r Sys.Date()`" 
weight = 3
chapter = false
pre = "<b>3.3 </b>"
+++

Ở mục trước chúng ta đã tải lên vscode ssh server dưới quyền ubuntu và unzip file thành một thư mục trong /root

![image.png](/images/3-implementproject/3.3-febe/image.png?featherlight=false&width=60pc)

Chúng ta sẽ tiến hành triển khai Frontend và backend

### 1. Triển khai Backend

Chúng ta sẽ cd vào mục cd ecommerce-fullstack-netcore-react/backend/ và gõ

```bash
dotnet restore
```

Lệnh này dùng để cài đặt nhưng dependency cần thiết.

![image.png](/images/3-implementproject/3.3-febe/image1.png?featherlight=false&width=60pc)

Sau đó chúng ta khởi chạy dự Backend lên bằng lệnh sau đây

```bash
dotnet run
```

CLI báo rằng Backend đã được khởi động ở port 5214

![image.png](/images/3-implementproject/3.3-febe/image2.png?featherlight=false&width=60pc)
Chúng ta sẽ truy cập port 5214/swagger ở browser để kiểm tra kết quả chạy và kết qủa chạy thành công!

![image.png](/images/3-implementproject/3.3-febe/image3.png?featherlight=false&width=60pc)

Chúng ta sẽ kiểm tra thử một vài chức năng

![image.png](/images/3-implementproject/3.3-febe/image4.png?featherlight=false&width=60pc)

Chúng ta đã bị lỗi do chưa thiết lập database. Phần này mục sau bạn sẽ được thực hành!

Ở trên chúng ta đang dùng câu lệnh và khi mà chúng ta thoát ra ngoài bằng Ctrl + C thì backend sẽ không còn hoạt động nữa.

![image.png](/images/3-implementproject/3.3-febe/image5.png?featherlight=false&width=60pc)

Chúng ta sẽ tiến hành cho backend chạy dưới nền bằng cách 

```bash
nohup dotnet run > run_bg.txt 2>&1 &
```

![image.png](/images/3-implementproject/3.3-febe/image6.png?featherlight=false&width=60pc)

Và kết quả là swagger vẫn chạy bình thường!

![image.png](/images/3-implementproject/3.3-febe/image7.png?featherlight=false&width=60pc)

### 2. Triển khai Frontend

Chúng ta tiến hành vào thư mục frontend 

```bash
cd /root/ecommerce-fullstack-netcore-react/frontend/
```

![image.png](/images/3-implementproject/3.3-febe/image17.png?featherlight=false&width=60pc)

Chúng ta sẽ tiến hành cài đặt thư viện câu lệnh sau:

```bash
npm install
```

{{% notice info %}}
🡇 Nội dung 🡇
{{% /notice %}}
Nếu bạn gặp vấn đề chạy `npm i` không được

![image.png](/images/3-implementproject/3.3-febe/image8.png?featherlight=false&width=60pc)

Ở chúng ta cần chạy câu lệnh sau:

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

Và chúng ta đã fix thành công!

![image.png](/images/3-implementproject/3.3-febe/image9.png?featherlight=false&width=60pc)
{{% notice info %}}
🡅 Nội dung 🡅
{{% /notice %}}

Chúng ta sẽ tiến hành chạy dự án frontend lên

```bash
npm start
```

![image.png](/images/3-implementproject/3.3-febe/image10.png?featherlight=false&width=60pc)

Sau khi khởi chạy thì chúng ta sẽ kiểm tra port 3000 ở browser và website đã khởi chạy thành công!

![image.png](/images/3-implementproject/3.3-febe/image11.png?featherlight=false&width=60pc)

![image.png](/images/3-implementproject/3.3-febe/image12.png?featherlight=false&width=60pc)

Nhưng ở đây giống như backend có một vấn đề là website đang chạy nếu chúng ta Ctrl + C thì website sẽ dùng lại ngay lập tức. Giải pháp là chúng ta sẽ cho chúng chạy trong nền. Ở bước này bạn có thể sử dụng nohup giống như trên.

Còn một giải pháp khác chúng ta có thể tiếp cận đó là pm2.

Chúng ta tiến hành tải pm2 về. Banj có thể tham khảo link tải ở đây: [PM2 - Quick Start (keymetrics.io)](https://pm2.keymetrics.io/docs/usage/quick-start/)

```bash
npm install pm2@latest -g
```

![image.png](/images/3-implementproject/3.3-febe/image13.png?featherlight=false&width=60pc)

Sau đó khởi dự án FE bằng pm2

```bash
pm2 start npm --name "ecommerce" -- run "start"
```

![image.png](/images/3-implementproject/3.3-febe/image14.png?featherlight=false&width=60pc)

Và kết quả là website vẫn chạy bình thường. Ở bên dưới do chúng ta chưa thiết lập Sql nên các sản phẩm chưa hiển thị ra

![image.png](/images/3-implementproject/3.3-febe/image15.png?featherlight=false&width=60pc)

Ngoài ra pm2 có có chức năng monitoring

```bash
pm2 monit
```

![image.png](/images/3-implementproject/3.3-febe/image16.png?featherlight=false&width=60pc)

Vậy là chúng ta đã triển khai thành công Frontend và Backend.