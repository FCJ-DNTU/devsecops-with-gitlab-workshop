+++
title = "Giới thiệu cách triển khai bảo mật"
date = "`r Sys.Date()`" 
weight = 5
chapter = false
pre = "<b>1.5 </b>"
+++

Khi triển khai bảo mật trong quy trình DevOps, có nhiều cách khác nhau áp dụng tại các giai đoạn khác nhau của sản phẩm.

### 1. Quản lý người dùng và quyền truy cập

- **Sử dụng xác thực hai yếu tố (2FA):** Yêu cầu người dùng kích hoạt 2FA để tăng cường bảo mật tài khoản.
- **Quản lý quyền truy cập:** Phân quyền chặt chẽ theo vai trò (Owner, Maintainer, Developer, v.v.) để đảm bảo người dùng chỉ có quyền truy cập vào những gì họ cần.
- **Sử dụng nhóm và dự án riêng tư:** Đặt các dự án và nhóm ở chế độ riêng tư để tránh truy cập không được phép từ bên ngoài.

### 2. Bảo vệ mã nguồn

- **Mã hóa repository:** Đảm bảo rằng mã nguồn trong các repository được mã hóa, đặc biệt là khi lưu trữ trên các hệ thống lưu trữ bên ngoài.
- **Sử dụng Git Hooks:** Thiết lập Git hooks để thực hiện các kiểm tra bảo mật, như cấm đẩy mật khẩu hoặc thông tin nhạy cảm vào repository.

### 3. Bảo mật CI/CD Pipeline

- **Sử dụng các runner tự quản lý (self-hosted runner):** Điều này giúp kiểm soát tốt hơn môi trường thực thi và tránh rủi ro khi sử dụng runner công cộng.
- **Thiết lập các biến môi trường an toàn:** Sử dụng các biến môi trường (environment variables) trong CI/CD pipeline và bảo vệ chúng bằng cách đánh dấu là "protected" hoặc "masked".
- **Kiểm tra bảo mật tự động:** Tích hợp các công cụ kiểm tra bảo mật (như SAST, DAST, Dependency Scanning) vào pipeline để tự động phát hiện lỗ hổng bảo mật trong mã nguồn.

### 4. Giám sát và kiểm toán

- **Kích hoạt logging và auditing:** Ghi lại mọi hành động quan trọng trong GitLab để có thể theo dõi và kiểm tra khi cần thiết.
- **Thiết lập cảnh báo bảo mật:** Sử dụng các công cụ giám sát để nhận cảnh báo khi có hành vi bất thường hoặc khi phát hiện lỗ hổng bảo mật.

### 5. Cấu hình GitLab bảo mật

- **Cập nhật thường xuyên:** Đảm bảo rằng GitLab luôn được cập nhật lên phiên bản mới nhất để vá các lỗ hổng bảo mật đã biết.
- **Sử dụng HTTPS và SSL/TLS:** Đảm bảo rằng tất cả các kết nối tới GitLab đều được mã hóa qua HTTPS.
- **Cấu hình tường lửa và IP Whitelisting:** Giới hạn truy cập GitLab chỉ từ các địa chỉ IP tin cậy.

Trong lab này chúng ta sẽ đi sâu vào **CI/CD Pipeline Security,** cụ thể là Kiểm tra bảo mật tự động thông qua các công cụ của GitLab Template hoặc các công cụ open-source.

**GitLab Templates:**

- **Tính tiện lợi và dễ sử dụng:** GitLab templates là các cấu hình sẵn có được GitLab cung cấp, giúp dễ dàng tích hợp các công cụ bảo mật vào CI/CD pipeline. Những template này đã được tối ưu hóa và kiểm thử để làm việc liền mạch với GitLab, giúp giảm thời gian cấu hình và triển khai.
- **Tính tích hợp sẵn có:** Các GitLab templates thường đi kèm với các tính năng bảo mật như SAST (Static Application Security Testing), DAST (Dynamic Application Security Testing), Dependency Scanning, và Secret Detection. Chúng có thể được thêm vào pipeline chỉ bằng cách tham chiếu đến các templates này trong file `.gitlab-ci.yml`.
- **Hỗ trợ và cập nhật:** Vì là sản phẩm chính thức của GitLab, các templates này thường nhận được hỗ trợ kỹ thuật và cập nhật bảo mật kịp thời từ GitLab, đảm bảo rằng bạn luôn sử dụng phiên bản mới nhất và an toàn nhất của các công cụ bảo mật.
- **Tính mở rộng hạn chế:** Mặc dù GitLab templates rất tiện lợi, nhưng khả năng tùy chỉnh có thể bị hạn chế nếu bạn cần một cấu hình rất đặc thù hoặc cần tích hợp các công cụ bảo mật không được GitLab hỗ trợ trực tiếp.

**Công cụ bảo mật mã nguồn mở (Open-source Tools):**

- **Tính linh hoạt và tùy chỉnh cao:** Công cụ mã nguồn mở cho phép bạn tùy chỉnh gần như hoàn toàn theo yêu cầu cụ thể của dự án. Bạn có thể lựa chọn, cấu hình và tích hợp các công cụ bảo mật một cách chi tiết theo nhu cầu.
- **Sự đa dạng và khả năng mở rộng:** Có rất nhiều công cụ mã nguồn mở cho bảo mật phần mềm, ví dụ như SonarQube cho phân tích mã nguồn, OWASP ZAP cho kiểm thử bảo mật ứng dụng web, Trivy cho quét lỗ hổng trong containers, v.v. Bạn có thể lựa chọn công cụ phù hợp nhất cho từng trường hợp sử dụng cụ thể.
- **Chi phí:** Công cụ mã nguồn mở thường không yêu cầu chi phí bản quyền, giúp giảm chi phí triển khai, đặc biệt hữu ích cho các dự án nhỏ hoặc tổ chức phi lợi nhuận.
- **Tích hợp phức tạp:** Tích hợp các công cụ bảo mật mã nguồn mở đòi hỏi sự hiểu biết sâu hơn về cách chúng hoạt động, cũng như kỹ năng kỹ thuật để cấu hình chúng trong GitLab CI/CD. Điều này có thể tốn nhiều thời gian hơn và yêu cầu sự hỗ trợ từ đội ngũ phát triển.
- **Cập nhật và bảo trì:** Với các công cụ mã nguồn mở, bạn cần tự theo dõi và áp dụng các bản cập nhật, đồng thời chịu trách nhiệm bảo trì và sửa lỗi nếu cần. Điều này có thể yêu cầu thêm nguồn lực và thời gian.
