+++
title = "Giới thiệu về Devsecops"
date = "`r Sys.Date()`" 
weight = 4
chapter = false
pre = "<b>1.4 </b>"
+++

### 1. Các môi trường hiện tại

Các doanh nghiệp và công ty hiện tại đang có 3 môi trường chính:

- Development
- Pre-production
- Production

#### 1.1 Development Environment (Môi trường phát triển)

**Mục đích:** Đây là môi trường mà các nhà phát triển làm việc trực tiếp để viết mã, phát triển các tính năng mới, sửa lỗi, và thực hiện các thay đổi cần thiết cho phần mềm.

**Đặc điểm:**

- Thường chứa mã nguồn chưa hoàn chỉnh hoặc đang trong quá trình phát triển.
- Các công cụ phát triển, gỡ lỗi, và mô phỏng thường được sử dụng tại đây.
- Môi trường này có thể không ổn định và không cần phải tuân thủ các tiêu chuẩn chất lượng cao như môi trường sản xuất.
- Thường sử dụng các cơ sở dữ liệu hoặc dịch vụ giả lập hoặc có dữ liệu không phải thật (dummy data).

**Ví dụ:** Khi một lập trình viên đang thêm một tính năng mới hoặc sửa một lỗi, họ sẽ làm việc trong môi trường phát triển để kiểm tra xem mã của họ có hoạt động như mong đợi không.

#### 1.2 Pre-production Environment (Môi trường tiền sản xuất)

**Mục đích:** Đây là môi trường để kiểm thử tổng thể trước khi mã nguồn được triển khai vào môi trường sản xuất. Môi trường này thường mô phỏng rất sát với môi trường sản xuất.

**Đặc điểm:**

- Được sử dụng để kiểm thử toàn diện, bao gồm kiểm thử tích hợp, kiểm thử hệ thống, và kiểm thử chấp nhận.
- Dữ liệu và cấu hình trong môi trường này thường tương tự hoặc giống với môi trường sản xuất, nhưng không phải là dữ liệu thật.
- Môi trường này thường được sử dụng để phát hiện các lỗi hoặc vấn đề có thể không xuất hiện trong môi trường phát triển.
- Các bản build ở đây thường đã trải qua quá trình kiểm thử trong môi trường phát triển và sẵn sàng để kiểm tra lần cuối trước khi triển khai vào sản xuất.

**Ví dụ:** Trước khi triển khai một phiên bản phần mềm mới, nhóm phát triển sẽ triển khai mã nguồn vào môi trường tiền sản xuất để kiểm tra tính tương thích, hiệu năng, và khả năng đáp ứng của hệ thống dưới tải trọng tương tự như môi trường sản xuất.

#### 1.3 Production Environment (Môi trường sản xuất)

**Mục đích:** Đây là môi trường mà phần mềm chạy thực tế và phục vụ người dùng cuối. Đây là môi trường quan trọng nhất và yêu cầu sự ổn định, bảo mật và hiệu suất cao.

**Đặc điểm:**

- Mã nguồn ở đây đã được kiểm tra kỹ lưỡng và sẵn sàng để sử dụng bởi khách hàng hoặc người dùng cuối.
- Môi trường này chứa dữ liệu thực, các giao dịch và thông tin nhạy cảm, do đó yêu cầu bảo mật cao.
- Tính khả dụng (availability) và hiệu suất (performance) là ưu tiên hàng đầu, vì bất kỳ sự cố nào cũng có thể ảnh hưởng trực tiếp đến người dùng và doanh thu của doanh nghiệp.
- Các thay đổi trong môi trường sản xuất thường được quản lý chặt chẽ và triển khai qua các quy trình nghiêm ngặt để giảm thiểu rủi ro.

**Ví dụ:** Khi một phiên bản phần mềm mới đã vượt qua tất cả các bài kiểm thử ở môi trường tiền sản xuất, nó sẽ được triển khai vào môi trường sản xuất để người dùng cuối có thể truy cập và sử dụng.

### 2. Các cách triển khai dự án

Ba cách triển khai dự án phổ biến hiện nay gồm:

- Service
- Container Standalone
- Container Orchestration

#### 2.1 Service (Dịch vụ truyền thống)

Đây là phương pháp triển khai phần mềm truyền thống, trong đó các ứng dụng được cài đặt trực tiếp trên máy chủ vật lý hoặc máy chủ ảo (VMs).

Các dịch vụ như web server (Apache, Nginx), cơ sở dữ liệu (MySQL, PostgreSQL), và các ứng dụng khác được cài đặt, cấu hình, và quản lý trực tiếp trên hệ điều hành của máy chủ.

**Ví dụ:** Một ứng dụng web được triển khai trên một máy chủ chạy Nginx, với cơ sở dữ liệu MySQL cài đặt trên cùng một máy chủ hoặc máy chủ khác.

#### 2.2 Container Standalone (Container độc lập)

Container là một phương pháp đóng gói ứng dụng cùng với các phụ thuộc của nó vào một môi trường nhẹ, di động, có thể chạy nhất quán trên bất kỳ hệ điều hành nào hỗ trợ container.

Khi triển khai dưới dạng container standalone, ứng dụng được đóng gói trong một container (ví dụ: Docker) và chạy trên một máy chủ hoặc một nhóm máy chủ, nhưng không có công cụ điều phối để quản lý container này.

**Ví dụ:** Một ứng dụng web được đóng gói trong một container Docker và triển khai trên một máy chủ duy nhất, quản lý thủ công các container này.

#### 2.3 Container Orchestration (Điều phối container)

Điều phối container là phương pháp quản lý và điều phối các container trên một hoặc nhiều máy chủ, thường sử dụng các công cụ như Kubernetes, Docker Swarm, hoặc Apache Mesos.

Hệ thống điều phối quản lý các tác vụ như triển khai, mở rộng, cân bằng tải, và phục hồi container khi gặp sự cố.

**Ví dụ:** Một ứng dụng microservices bao gồm nhiều container (cơ sở dữ liệu, API, frontend) được triển khai trên Kubernetes. Kubernetes sẽ quản lý việc triển khai, mở rộng, và phục hồi của các container này trên một cluster gồm nhiều máy chủ.

### 3. Các phương pháp triển khai

Hiện có 2 phương pháp triển khai:

- Build và Deploy trên cùng server
- Build và Deploy trên khác server

#### 3.1 Build và Deploy trên cùng một server

Trong phương pháp này, quá trình build (xây dựng mã nguồn thành ứng dụng sẵn sàng để chạy) và deploy (triển khai ứng dụng lên môi trường sản xuất) được thực hiện trên cùng một máy chủ.

Mã nguồn được kiểm tra (checkout) trực tiếp trên máy chủ này, sau đó quá trình build được thực hiện tại chỗ, và ngay sau đó ứng dụng được triển khai trực tiếp trên máy chủ đó.

**Ví dụ:** Một trang web đơn giản được triển khai trên một máy chủ duy nhất, nơi các nhà phát triển đẩy mã nguồn, xây dựng, và triển khai trực tiếp trên server này.

#### 3.2 Build và Deploy ở hai server khác nhau

Với phương pháp này, quá trình build được thực hiện trên một máy chủ riêng biệt, và sau khi quá trình build hoàn tất, ứng dụng sẽ được chuyển giao để deploy trên một (hoặc nhiều) máy chủ khác.

Máy chủ build (build server) thường là một hệ thống chuyên dụng với các công cụ cần thiết để xây dựng mã nguồn, trong khi máy chủ deploy (deployment server) là nơi ứng dụng chạy trong môi trường sản xuất.

**Ví dụ:** Một ứng dụng doanh nghiệp lớn với nhiều dịch vụ microservices, nơi quá trình build diễn ra trên các server CI/CD chuyên dụng như Jenkins, và sau đó ứng dụng được deploy trên các máy chủ khác nhau (ví dụ: môi trường staging hoặc production).

### 3.3 Thành phần cơ bản của Pipeline DevSecOps

- **Build:** Xây dựng và biên dịch mã nguồn thành các Artifact hoặc Registry có thể triển khai được.
- **Test:** Đảm bảo mã nguồn hoạt động như mong đợi và không gây ra lỗi.
- **Deploy:** Đưa các Artifact hoặc Registry đã xây dựng lên môi trường production.

### 4. Quy trình DevSecOps chung

![image.png](/images/1-introduction/1.4-devsecops/image.png?featherlight=false&width=90pc)

Có 5 giai đoạn chính:

- LOCAL: Đây là giai đoạn trong môi trường tại máy tính của mình.
- DEV: Đây là giai đoạn phát triển ứng dụng.
- REPOSITORY: Đây là giai đoạn giúp lưu trữ các bản build của dự án.
- PRE-PROD: Đây là giai đoạn trước khi phát hành sản phẩm ra ngoài thực tế.
- PROD: Đây là giai đoạn gần người dùng cuối nhất, cho ra những sản phẩm thực tế mà người dùng được sử dụng.

#### 4.1 Giai đoạn local

![image.png](/images/1-introduction/1.4-devsecops/image1.png?featherlight=false&width=10pc)

Đầu tiên, một dev sau khi code các tính năng xong thì họ sẽ phải commit code của họ lên Git Server.

Khi đó `PCSS` (Pre-Commit Configuration for Secret Scanning) sẽ quét bí mật trước khi commit. Nó đóng vai trò trung gian giúp thiết lập cấu hình để quét mã nguồn cho các thông tin nhạy cảm trước khi commit vào repo.

Ví dụ: Cài đặt công cụ như Git-secrets, TruffleHog, Talisman để quét mã nguồn trước khi cam kết vào kho mã nhằm phát hiện các thông tin nhạy cảm như mật khẩu, khóa API.

Sau khi qua kiểm tra, mã nguồn mới được commit vào repo trên Git Server.

#### 4.2 Giai đoạn dev

![image.png](/images/1-introduction/1.4-devsecops/image2.png?featherlight=false&width=10pc)

Khi branch dev nhận các mã nguồn từ các branch feature như mô hình GitFlow, chúng ta sẽ thực hiện SAST (Static Application Security Testing).

SAST là bước kiểm tra bảo mật mã nguồn tĩnh để phát hiện các lỗ hổng bảo mật trước khi xây dựng (build) và triển khai (deploy). SAST phân tích mã nguồn mà không thực thi mã.

Các công cụ phổ biến:

  - SonarQube
  - Fortify
  - Checkmarx
  - Veracode

Sau khi hoàn thành SAST, tiếp theo là bước SCA (Software Composition Analysis).

SCA kiểm tra các thành phần bên thứ ba trong mã nguồn để tìm lỗ hổng bảo mật và đảm bảo tuân thủ các chính sách.

Ví dụ: sử dụng công cụ như Snyk hoặc OWASP Dependency-Check để kiểm tra các thư viện và thành phần bên thứ ba có lỗ hổng bảo mật.

Sau đó, tiến hành bước Build (cũng có thể triển khai ngay tại bước này và thực hiện unit test cho ứng dụng).

#### 4.3 Giai đoạn repository

![image.png](/images/1-introduction/1.4-devsecops/image3.png?featherlight=false&width=10pc)

Các bản build sau khi hoàn tất sẽ được lưu trữ trong Artifact (ví dụ: Azure Artifacts, JFrog Artifactory, Nexus Repository) nếu được triển khai theo dạng service. Nếu triển khai theo Kubernetes hoặc container standalone, các bản build sẽ được lưu trong Container Registry (ví dụ: ECR, Docker Hub, GCR).

Khi lưu vào, cần thực hiện bước Image Scan để đảm bảo rằng các hình ảnh container và các thành phần khác không chứa lỗ hổng bảo mật.

Ví dụ: sử dụng công cụ như Docker Bench for Security để kiểm tra các hình ảnh Docker trước khi triển khai.

#### 4.4 Giai đoạn Pre-Prod

![image.png](/images/1-introduction/1.4-devsecops/image4.png?featherlight=false&width=10pc)

Tại đây, sau khi quét xong các image từ giai đoạn trước, ứng dụng sẽ được deploy trên container.
Sau khi deploy, thực hiện bước DAST (Dynamic Application Security Testing).

DAST kiểm tra bảo mật bằng cách tương tác với ứng dụng trong môi trường hoạt động của nó, thường là thông qua trình duyệt web hoặc giao diện người dùng.

DAST mô phỏng các cuộc tấn công vào ứng dụng từ bên ngoài, giống như một hacker sẽ làm, và phân tích các phản hồi từ ứng dụng để phát hiện các lỗ hổng bảo mật.

Công cụ DAST phổ biến: OWASP ZAP (Zed Attack Proxy), Burp Suite, Acunetix.

Sau khi hoàn thành bước DAST, tiến hành Test Performance. Bước này kiểm tra tài nguyên có quá lớn hay không và tối ưu hóa tài nguyên từ các image để tăng hiệu suất deploy và hoạt động.

#### 4.5 Giai đoạn Prod

![image.png](/images/1-introduction/1.4-devsecops/image5.png?featherlight=false&width=10pc)

Deploy ứng dụng thực tế trên Kubernetes (k8s). Kubernetes giúp kết nối các container lại với nhau và quản lý chúng một cách dễ dàng hơn.

Sau khi deploy, thực hiện bước Pen Test (Penetration Testing).Pen Test kiểm tra bảo mật toàn diện bằng cách mô phỏng các cuộc tấn công thực tế từ bên ngoài để tìm kiếm và khai thác lỗ hổng bảo mật. Điều này bao gồm cả ứng dụng web, hệ thống mạng, cơ sở hạ tầng và các thiết bị khác.

Cuối cùng, thực hiện Monitoring ứng dụng và gửi báo cáo để theo dõi tình trạng của ứng dụng.

### 5. Tóm lại

Đây không phải là tất cả các quy trình có thể kết hợp trong mọi tình huống, vì mỗi doanh nghiệp sẽ có những cách triển khai khác nhau và có thể thiếu một vài bước trong quy trình.


Quy trình đầy đủ: Commit → PCSS → SAST → SCA → Build → Deploy for Unit Testing → Artifact → Image Scan → Deploy Pre-Prod → DAST → Performance → Deploy Prod → Pen Test → Report

Doanh nghiệp A: Commit → PCSS → SAST → Build → Deploy Prod

Doanh nghiệp B: Commit → Build → Artifact → Image Scan → Deploy Prod → Report

Nói chung, bạn cần xem xét kỹ lưỡng tài liệu và khả năng áp dụng các bước trong quy trình của doanh nghiệp và đội ngũ để đảm bảo phù hợp với tình hình hiện tại. Ví dụ, nếu ứng dụng của bạn nhỏ và chưa có nhiều người dùng, và chỉ triển khai trên hạ tầng mạng đơn giản, có thể lược bỏ một số bước như Performance, Pen Test, DAST, Deploy for Unit Testing, để dễ dàng áp dụng hơn.
