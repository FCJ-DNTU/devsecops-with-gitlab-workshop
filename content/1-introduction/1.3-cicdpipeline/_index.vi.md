+++
title = "Giới thiệu quy trình CI/CD"
date = "`r Sys.Date()`" 
weight = 3
chapter = false
pre = "<b>1.3 </b>"
+++

### 1. Tại sao lại là CI/CD?

Hãy tưởng tượng bạn đang là một DevOps trong một doanh nghiệp. Vào một ngày đẹp trời, bạn nhận 20 dự án đang cần bạn triển khai. Bạn làm tất cả bằng thủ công từ bước Deploy Frontend, Backend, Database, Build, Monitoring, chưa kể các bước Security… Bạn cần tốn thời gian từ 30 phút đến 1 giờ để hoàn thành việc triển khai. Điều này dẫn đến tiến độ của tất cả dự án bị chậm vì khi bạn triển khai một dự án thì những dự án còn lại phải chờ, các công việc Build và Deploy cứ thế lặp đi lặp lại.

Các DevOps tự hỏi: “Tại sao chúng ta không tạo ra một quy trình để tự động hóa Build, Testing, Deploy dựa trên các kịch bản có sẵn?” Và từ đó, khái niệm CI/CD ra đời.

### 2. CI/CD là gì?

**CI/CD** là một bộ đôi công việc, bao gồm **CI (Continuous Integration)** và **CD (Continuous Delivery)** hoặc **Continuous Deployment (Triển khai liên tục)**, ý nói là quá trình tích hợp (integration) thường xuyên, nhanh chóng hơn khi code cũng như thường xuyên cập nhật phiên bản mới (delivery).

![image.png](/images/1-introduction/1.3-cicdpipeline/image.png?featherlight=false&width=90pc)

#### 2.1 Continuous Integration (CI)

Continuous Integration (CI) là một phương pháp trong phát triển phần mềm, trong đó các developer liên tục tích hợp (merge) mã nguồn mới vào một nhánh chính, thường là nhánh `develop` hoặc `master`. Mỗi khi mã mới được tích hợp, hệ thống CI sẽ tự động xây dựng (build) dự án và chạy các bộ kiểm thử (tests) để đảm bảo rằng mã mới không gây ra lỗi hoặc làm gián đoạn hệ thống hiện tại.

Mục tiêu chính của CI là phát hiện lỗi sớm trong quá trình phát triển, giảm thiểu rủi ro và đảm bảo rằng phần mềm luôn ở trạng thái có thể phát hành được. Một hệ thống CI điển hình bao gồm các bước như:

1. **Commit Code:** Các nhà phát triển thường xuyên commit mã của họ vào hệ thống kiểm soát phiên bản (như Git).
2. **Build Automation:** Hệ thống CI sẽ tự động build mã sau mỗi lần commit để kiểm tra xem có bất kỳ lỗi nào trong quá trình build hay không.
3. **Automated Testing:** Sau khi build, hệ thống CI chạy các bài kiểm thử tự động (unit tests, integration tests, etc.) để đảm bảo rằng mã mới không phá vỡ chức năng hiện có.
4. **Feedback:** Nếu build hoặc tests thất bại, hệ thống CI sẽ cung cấp phản hồi ngay lập tức cho các nhà phát triển để họ có thể sửa chữa lỗi nhanh chóng.

#### 2.2 Continuous Delivery (CD)

Continuous Delivery (CD) là một phương pháp trong phát triển phần mềm, trong đó các thay đổi mã nguồn (tính năng mới, cải tiến, sửa lỗi) được tự động xây dựng, kiểm thử, và chuẩn bị để phát hành vào môi trường sản xuất một cách nhanh chóng và nhất quán. CD mở rộng từ Continuous Integration (CI) và nhấn mạnh việc đưa phần mềm đến trạng thái mà nó có thể được phát hành bất kỳ lúc nào.

Mục tiêu chính của Continuous Delivery là đảm bảo rằng mã nguồn luôn ở trạng thái sẵn sàng phát hành, nghĩa là mã đã được xây dựng, kiểm thử và chuẩn bị sẵn sàng để triển khai chỉ với một nút bấm.

#### 2.3 Continuous Deployment (CD)

**Continuous Deployment** là một phần mở rộng của Continuous Delivery (CD), trong đó các thay đổi mã nguồn được tự động triển khai vào môi trường sản xuất ngay lập tức sau khi chúng vượt qua tất cả các bước kiểm thử. Không cần sự can thiệp của con người, mỗi thay đổi mã (sau khi đã qua kiểm thử tự động) sẽ được đưa thẳng vào sản phẩm đang hoạt động.

Trong Continuous Deployment, mọi commit đã vượt qua quá trình kiểm thử đều được tự động triển khai vào sản xuất. Điều này giúp phần mềm được cập nhật liên tục, mang lại các tính năng mới, sửa lỗi, và cải tiến hiệu suất ngay khi chúng sẵn sàng.

### 3. Lợi ích của CI/CD

- Tự động hóa công việc
- Thống nhất các quy trình
- Release nhanh chóng
- Đảm bảo chất lượng
- Phản hồi nhanh
- Phát hiện lỗi nhanh chóng

### 4. Các thành phần chính của CI/CD Pipeline

![image.png](/images/1-introduction/1.3-cicdpipeline/image1.png?featherlight=false&width=90pc)

1. **Source Stage (Giai đoạn mã nguồn):** Giai đoạn này bắt đầu khi một nhà phát triển commit mã nguồn mới vào hệ thống quản lý mã nguồn (như Git). Việc commit này sẽ kích hoạt pipeline CI/CD.
2. **Build Stage (Giai đoạn xây dựng):** Mã nguồn được biên dịch và xây dựng thành các gói ứng dụng hoặc container. Giai đoạn này đảm bảo rằng mã nguồn có thể được xây dựng thành công và sẵn sàng cho các bước kiểm tra tiếp theo.
3. **Test Stage (Giai đoạn kiểm tra):** Mã nguồn được kiểm tra bằng cách chạy các bài kiểm tra tự động như unit tests, integration tests, và end-to-end tests. Giai đoạn này nhằm đảm bảo rằng mã nguồn không có lỗi và hoạt động như mong đợi.
4. **Deploy Stage (Giai đoạn triển khai):** Mã nguồn đã qua kiểm tra được triển khai vào các môi trường staging hoặc production. Triển khai có thể được thực hiện tự động (continuous deployment) hoặc yêu cầu sự chấp thuận của con người (continuous delivery).

Ví dụ về CI/CD Pipeline:

- Developer Commit: Nhà phát triển commit mã nguồn mới vào GitLab.
- Automated Build: Hệ thống CI/CD tự động biên dịch mã nguồn và xây dựng thành gói ứng dụng.
- Automated Testing: Hệ thống chạy các bài kiểm tra tự động để đảm bảo mã nguồn không có lỗi.
- Staging Deployment: Mã nguồn đã kiểm tra được triển khai vào môi trường staging để kiểm tra cuối cùng.
- Production Deployment: Nếu mã nguồn vượt qua tất cả các bài kiểm tra, nó được triển khai vào môi trường production.
- Monitoring and Feedback: Hệ thống giám sát và thu thập phản hồi từ người dùng để cải thiện các phiên bản tiếp theo.

### 5. Các công cụ CI/CD

- GitLab
- Jenkins
- Argo

### 6. GitLab CI/CD

#### 6.1 Thành phần

![image.png](/images/1-introduction/1.3-cicdpipeline/image2.png?featherlight=false&width=90pc)

GitLab CI/CD có hai thành phần chính:

- Runner: Bạn có thể xem đây là một robot có chức năng thực thi các nhiệm vụ đã được lên kịch bản từ trước.
- YAML: File kịch bản mà Runner sẽ đọc và thực hiện hành động.

#### 6.2 Quy trình CI/CD tích hợp GitLab

Đây là một quy trình sử dụng CI/CD tích hợp GitLab

![image.png](/images/1-introduction/1.3-cicdpipeline/image3.png?featherlight=false&width=90pc)

- Đầu tiên lập trình viên push code vào nhánh develop, khi đó GitLab Runner sẽ kích hoạt đọc file `.gitlab-ci.yml`.
- Trong bước CI, GitLab Runner đang thực hiện các bước từ chuẩn bị, build, test theo kịch bản trong xây dựng.
- Chúng ta có thể sử dụng Shared Runner (một runner có thể chạy được nhiều dự án) và Specific Runner (một Runner chạy được một dự án) và có tích hợp các phần như container, artifact, …
- Cuối cùng là deploy lên môi trường production.

![image.png](/images/1-introduction/1.3-cicdpipeline/image4.png?featherlight=false&width=90pc)

Tưởng tượng bên GitLab Runner thì bên Jenkins cũng sẽ có Jenkins Agent như hình dưới đây

![image.png](/images/1-introduction/1.3-cicdpipeline/image5.png?featherlight=false&width=90pc)

### Tham khảo

[CI/CD là gì? Triển khai quy trình CI CD với Gitlab (topdev.vn)](https://topdev.vn/blog/trien-khai-ci-cd-voi-gitlab/#continuous-integration-ci)