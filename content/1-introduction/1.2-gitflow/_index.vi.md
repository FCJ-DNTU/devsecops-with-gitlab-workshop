+++
title = "Giới Thiệu về Gitflow"
date = "`r Sys.Date()`" 
weight = 2
chapter = false
pre = "<b>1.2 </b>"
+++

### 1. Vấn đề

Hàng ngày, công việc của chúng ta thường xuyên tiếp xúc với Git, thế nên chắc hẳn mọi người đều biết trong Git có các khái niệm về nhánh (branch) cùng các lệnh xử lý nhánh như checkout, merge, hay là revert…

Khi phát triển thêm một tính năng mới, chúng ta thường checkout từ nhánh hiện tại sang một nhánh khác để làm việc. Môi trường làm việc nhóm sẽ có nhiều người phát triển nhiều tính năng, mỗi tính năng nằm trên mỗi nhánh, chúng có thể phát triển độc lập và song song với nhau. Mọi thứ sẽ thật hoàn hảo nếu như một ngày đẹp trời khi tiến hành merge các nhánh lại với nhau mà không hề xuất hiện bất kỳ xung đột (conflict) nào.

Gitflow được sinh ra để quy định những nguyên tắc trong phân nhánh và phát triển tính năng cũng như phát hành sản phẩm. Đó là một quy trình làm việc với Git sao cho thuận tiện nhất cho những người phát triển.

### 2. Gitflow là gì?

Gitflow là khái niệm chỉ cách phân nhánh và phối hợp phát triển (development), phát hành (release) tính năng bằng cách sử dụng Git.

> *Gitflow được biến tấu theo từng nhóm phát triển, tùy vào môi trường và điều kiện thì người quản lý có thể đặt ra những quy tắc về luồng phát triển ứng dụng. Có thể là tự nghĩ ra hoặc dựa trên một flow đã có từ trước.*

Thực tế có rất nhiều mô hình Gitflow được giới thiệu, tuy nhiên nổi tiếng nhất là flow dựa trên mô hình phân nhánh của Vincent Driessen.

### 3. Gitflow theo mô hình phân nhánh Vincent Driessen

![image.png](/images/1-introduction/1.2-gitflow/image.png?featherlight=false&width=40pc)

Mô hình Gitflow theo Vincent Driessen cơ bản sẽ như sau:

- Nhánh mở đầu sẽ là nhánh master (hoặc main), ở nhánh này chúng ta không nên sửa đổi bất kỳ file nào trừ khi triển khai một tag mới.
- Khi bắt đầu, chúng ta sẽ checkout từ nhánh master sang nhánh develop, ở đây là nhánh sẽ tổng hợp các code từ các nhánh feature/*.
- Mỗi tính năng sẽ checkout tiếp từ develop ra các nhánh có prefix là feature. (Ví dụ tính năng login là feature/login-ui, tính năng logout là feature/logout-ui…) và phát triển độc lập với nhau. Sau khi hoàn thành thì merge feature vào develop.
- Đến mỗi giai đoạn release, chúng ta sẽ merge code của nhánh develop vào nhánh release. Nhánh release đóng vai trò như là môi trường staging của dự án. Đây là môi trường gần với môi trường Production nhất. Nhánh release sẽ bao gồm các commit fix bug mà không bao gồm commit feature nào nữa. Trong trường hợp nếu có bug phát sinh trong nhánh release thì fix bug trong nhánh release và merge code về develop, tiếp tục phát triển thêm các feature mới.
- Khi đã xử lý xong hết bug ở nhánh release thì chúng ta sẽ tiến hành merge code vào nhánh master. Tại nhánh master chúng ta có thể đánh dấu tag cho commit đó để làm dấu hiệu phiên bản phát hành. Điều này sẽ có lợi cho việc backup code bởi vì chúng ta có thể quay về phiên bản cũ hơn. Thuận tiện cho quá trình truy vết và theo dõi code.
- Khi chạy production không thể tránh khỏi lỗi phát sinh, khi đó chúng ta sẽ checkout từ master ra nhánh có tiền tố hotfix/ (ví dụ chúng ta cần fix bug trong shopping cart thì sẽ đặt tên hotfix/1-shopping-cart, lưu ý 1 là id của Issue mà chúng ta tạo ra và gán label là bug).
- Khi code master được merge từ nhánh release, thì webhook của source code management app (Github, Gitlab, Aruze Devops,…) sẽ bắt sự kiện và kích hoạt CI/CD pipeline và triển khai production (ví dụ: deploy database → deploy backend → deploy frontend, sau đó monitoring + logging trong quá trình sản phẩm running). Nhiệm vụ này sẽ do các DevOps Engineer đảm nhận.

### Tham khảo

[Sử dụng Gitlab để quản lý mã nguồn dự án (notion.site)](https://www.notion.so/899f462b85224829837b3613408ccd54?pvs=21)