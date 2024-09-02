+++
title = "Làm quen với Gitlab"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>4.1 </b>"
+++

### 1. Tạo tài khoản Gitlab

Sau đây là hướng dẫn về truy cập và tạo tài khoản bằng Gitlab:

**Bước 1: Truy cập vào web Gitlab**

- Mở trình duyệt của bạn và truy cập vào trang chủ [gitlab.com](https://about.gitlab.com/)

**Bước 2: Đăng ký tài khoản**

- Click vào nút Sign in ở bên góc phải của trang chủ

![image.png](/images/4-setupgitlab/4.1-getstarted/image.png?featherlight=false&width=60pc)

- Chọn Register now để đăng ký tài khoản, hoặc có thể đăng nhập bằng tài khoản google, github, … nếu như đã có trước đó.

![image.png](/images/4-setupgitlab/4.1-getstarted/image1.png?featherlight=false&width=30pc)

**Bước 3: Điền thông tin đăng ký**

- Tên người dùng (Username): Chọn tên người dùng duy nhất cho tài khoản của bạn.
- Email: Nhập địa chỉ email của bạn.
- Mật khẩu (Password): Tạo một mật khẩu an toàn cho tài khoản của bạn.
- Xác nhận mật khẩu (Confirm Password): Nhập lại mật khẩu để xác nhận.
- Thông tin khác: Có thể sẽ có một số thông tin khác như "Tên" và "Họ".

![image.png](/images/4-setupgitlab/4.1-getstarted/image2.png?featherlight=false&width=30pc)

**Bước 4: Xác nhận và đăng ký**

- Xác nhận bằng cách click vào Verify

![image.png](/images/4-setupgitlab/4.1-getstarted/image3.png?featherlight=false&width=30pc)

- Sau đó xác nhận theo yêu cầu nếu đúng sẽ hiện ra hình sau

![image.png](/images/4-setupgitlab/4.1-getstarted/image4.png?featherlight=false&width=30pc)

- Sau khi xong các bước trên nhấn vào nút "Register" (Đăng ký) để tạo tài khoản.

**Bước 5:  Xác minh địa chỉ email**

- Sau khi đăng ký, GitLab sẽ gửi một email xác nhận đến địa chỉ email bạn đã cung cấp.

![image.png](/images/4-setupgitlab/4.1-getstarted/image5.png?featherlight=false&width=60pc)

- Mở email và nhấp vào liên kết xác minh để xác nhận địa chỉ email của bạn.

**Bước 6: Đăng nhập vào GitLab**

- Sau khi xác minh email, quay lại trang chủ GitLab và nhấn vào "Sign in" (Đăng nhập).
- Nhập tên người dùng hoặc email và mật khẩu của bạn, sau đó nhấn vào "Sign in".

![image.png](/images/4-setupgitlab/4.1-getstarted/image6.png?featherlight=false&width=60pc)

**Bước 7**: Bắt đầu với Gitlab

- Và bây giờ chúng ta có thể thao tác cấu hình trên nó, các hướng dẫn cụ thể hơn sẽ ở những phần tiếp theo

### 2. Làm quen với giao diện của Gitlab

Đầu tiên mình phải làm quan với giao diện của Gitlab đã, nếu như đây là lần đầu bạn dùng Gitlab, thì Gitlab sẽ bắt bạn tạo một project mới khi vào, ở đây mình tạo 1 group có tên là `ecommerce` và có project name là `frontend` , và ô bên dưới mình không tích chọn tạo file README. Nếu các bạn thích thì có thể sử dụng import (phần này mình sẽ nói thêm ở phần sau).

![image.png](/images/4-setupgitlab/4.1-getstarted/image7.png?featherlight=false&width=60pc)

Khi thực hiện tạo project thì đây là giao diện Dashboard của Gitlab

![image.png](/images/4-setupgitlab/4.1-getstarted/image8.png?featherlight=false&width=50pc)

![image.png](/images/4-setupgitlab/4.1-getstarted/image9.png?featherlight=false&width=20pc)

Phía bên trái là thanh sidebar của project `frontend` . Nó có các chức năng cơ bản như:

- **frontend**: Đây là tên của dự án của bạn.
- **Learn GitLab** (Học GitLab): Cung cấp tài nguyên và hướng dẫn để học cách sử dụng GitLab.
- **Pinned** (Ghim): Nơi bạn có thể ghim các mục quan trọng để truy cập nhanh chóng.
- **Issues** (Vấn đề): Hiển thị danh sách các vấn đề (công việc, lỗi, yêu cầu tính năng) liên quan đến dự án và **`0`** có nghĩa là hiện không có vấn đề nào đang mở.
- **Merge Requests** (Yêu cầu hợp nhất): Hiển thị danh sách các yêu cầu hợp nhất (merge requests) để hợp nhất mã từ các nhánh khác vào nhánh chính.
- **Manage** (Quản lý): Các công cụ và tùy chọn để quản lý dự án.
- **Plan** (Lên kế hoạch): Công cụ và tùy chọn để lên kế hoạch cho dự án, bao gồm các nhiệm vụ và milestone.
- **Code** (Mã nguồn): Nơi bạn có thể truy cập và quản lý mã nguồn của dự án.
- **Build** (Xây dựng): Công cụ để xây dựng và kiểm thử mã nguồn của dự án.
- **Secure** (Bảo mật): Các công cụ và tùy chọn để bảo mật mã nguồn và dự án.
- **Deploy** (Triển khai): Công cụ để triển khai mã nguồn của dự án lên môi trường sản xuất.
- **Operate** (Vận hành): Các công cụ để giám sát và vận hành dự án sau khi triển khai.
- **Monitor** (Giám sát): Các công cụ để giám sát hiệu suất và hoạt động của dự án.
- **Analyze** (Phân tích): Công cụ để phân tích dữ liệu và hiệu suất của dự án.
- **Setting** (Cài đặt): Đây là cài đặt cho project hiện tại

{{% notice info %}}
Các lựa chọn điều hướng trong bảng lựa chọn ở bên phải sẽ thay đổi, tùy thuộc vào đối tượng mà bạn đang xem. Đối tượng này có thể là một repository/project, group, sub-group.
{{% /notice %}}

![image.png](/images/4-setupgitlab/4.1-getstarted/image10.png?featherlight=false&width=60pc)

Ngoài ra thì bạn có thể setting account của mình bằng cách click vào edit profile 

![image.png](/images/4-setupgitlab/4.1-getstarted/image11.png?featherlight=false&width=20pc)

Các setting về account sẽ được hiển thị

![image.png](/images/4-setupgitlab/4.1-getstarted/image12.png?featherlight=false&width=50pc)