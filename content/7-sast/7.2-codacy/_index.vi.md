+++
title = "Codacy (Cloud)"
date = "`r Sys.Date()`" 
weight = 2
chapter = false
pre = "<b>7.2 </b>"
+++

Truy cập vào trang đăng nhập của codacy: [https://www.codacy.com](https://www.bing.com/ck/a?!&&p=df4d456eb1b9ea7bJmltdHM9MTcyNDI4NDgwMCZpZ3VpZD0xM2ZhODYzZS0yMjYwLTZiMGMtMTlmNy04OTU0MjNhMDZhMTAmaW5zaWQ9NTE5OA&ptn=3&ver=2&hsh=3&fclid=13fa863e-2260-6b0c-19f7-895423a06a10&psq=codacy&u=a1aHR0cHM6Ly93d3cuY29kYWN5LmNvbS8&ntb=1) và chọn đăng nhập với gitlab

![image.png](/images/7-sast/image%2022.png?featherlight=false&width=60pc)

Ủy quyền cho codacy

![image.png](/images/7-sast/image%2023.png?featherlight=false&width=60pc)

Đây là giao diện sao khi ủy quyền

![image.png](/images/7-sast/image%2024.png?featherlight=false&width=60pc)

Chọn add group ecommerce

![image.png](/images/7-sast/image%2025.png?featherlight=false&width=60pc)

Sau khi add group thì sẽ có 2 dự án chúng ta add tất cả

![image.png](/images/7-sast/image%2026.png?featherlight=false&width=60pc)

Sau khi add thì chúng ta sẽ có 2 dự án nhấp vào frontend

![image.png](/images/7-sast/image%2027.png?featherlight=false&width=60pc)

Tại dashboard frontend hiển thị tổng quan các lỗi

![image.png](/images/7-sast/image%2028.png?featherlight=false&width=60pc)

Tại mục Issue hiển thị chi tiết các lỗi bảo mật tùy theo mức độ

![image.png](/images/7-sast/image%2029.png?featherlight=false&width=60pc)

Di chuyển qua mục **Setting** → **Intergation** → chọn **Enable** tất cả

![image.png](/images/7-sast/image%2030.png?featherlight=false&width=60pc)

Khi mà chúng ta tạo 1 Merge từ các nhánh feature qua nhánh develop và chúng ta muốn codacy kích hoạt quét mã nguồn thì chúng ta sẽ enable nhánh develop lên

![image.png](/images/7-sast/image%2031.png?featherlight=false&width=60pc)

Tại mục dự án **frontend** chúng ta tạo ra một branch mới

![image.png](/images/7-sast/image%2032.png?featherlight=false&width=60pc)

Trong **Edit file** chúng ta copy thêm dòng import

![image.png](/images/7-sast/image%2033.png?featherlight=false&width=60pc)

Chúng ta tạo một **Merge Request** vào nhánh **develop**

![image.png](/images/7-sast/image%2034.png?featherlight=false&width=60pc)

Nhấp vào **Create Merge Request**

![image.png](/images/7-sast/image%2035.png?featherlight=false&width=60pc)

Khi tạo merge request thì một pipeline sẽ được chạy lên 

![image.png](/images/7-sast/image%2036.png?featherlight=false&width=60pc)

Nhấp vào pipeline đó để xem chi tiết

![image.png](/images/7-sast/image%2037.png?featherlight=false&width=60pc)

Gitlab sẽ điều hướng qua website codacy và codacy đã phát hiện ra vấn đề

![image.png](/images/7-sast/image%2038.png?featherlight=false&width=60pc)

Bạn có thể đọc thêm về codacy tại đây: [https://docs.codacy.com](https://www.bing.com/ck/a?!&&p=6e09d5cfeb919eb7JmltdHM9MTcyNDI4NDgwMCZpZ3VpZD0xM2ZhODYzZS0yMjYwLTZiMGMtMTlmNy04OTU0MjNhMDZhMTAmaW5zaWQ9NTE5OA&ptn=3&ver=2&hsh=3&fclid=13fa863e-2260-6b0c-19f7-895423a06a10&psq=codacy+docs&u=a1aHR0cHM6Ly9kb2NzLmNvZGFjeS5jb20v&ntb=1)