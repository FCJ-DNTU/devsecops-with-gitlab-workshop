+++
title = "Triển khai Performance Testing"
date = "`r Sys.Date()`" 
weight = 11
chapter = false
pre = "<b>11. </b>"
+++

Performance Testing (Kiểm thử hiệu suất) là quá trình đánh giá cách một ứng dụng hoạt động dưới các điều kiện tải khác nhau. Mục tiêu là để đảm bảo rằng ứng dụng có thể xử lý khối lượng công việc dự kiến và đáp ứng yêu cầu về hiệu suất trong môi trường sản xuất.

**Performance Testing (Kiểm thử hiệu năng) là một công đoạn trong việc kiểm thử phần mềm. Bước này sẽ dùng để kiểm tra nhiều khía cạnh của phần mềm** bao gồm những yếu tố:

- Tốc độ tải dữ liệu.
- Thời gian phần mềm phản hồi các truy cập
- Mức độ ổn định
- Mức độ tin cậy
- Có thể sử dụng các tài nguyên để nâng cao hiệu suất thực tế cho công việc hay không.

Performance Testing có ba trọng tâm chính là:

- Xác định xem ứng dụng có phản hồi nhanh chóng hay không
- Xác định xem tối đa phần mềm có thể tải được bao nhiêu người dùng
- Xác định mức độ ổn định của phần mềm khi được tải theo nhiều cách khác nhau

#### Tại sao phải kiểm thử hiệu năng?

Một hệ thống phần mềm bất kỳ hoạt động không chỉ dựa vào các tính năng có sẵn hỗ trợ mà còn dựa vào hiệu suất của ứng dụng. Performance testing là công đoạn đảm nhiệm vai trò giúp phát hiện và loại bỏ các vấn đề về hiệu suất chính xác nhất.

Việc kiểm thử hiệu năng sẽ xác định sự phù hợp các tính năng của ứng dụng với yêu cầu trong công việc và đảm bảo độ chính xác về thời gian hoạt động của các ứng dụng công nghệ trong thực tế.

Một số ví dụ về tầm quan trọng của kiểm thử hiệu năng:

Trong tổng số 500 công ty Fortune có 56% gặp vấn đề ngưng trệ thời gian hoạt động trung bình 1,6h/tuần gây tổn thất lên tới 896.000 đô la/tuần và hơn 46 triệu đô la/năm.

Google.com chỉ với 5 phút ngừng hoạt động vào thời gian ngày 19/8.2013 gây tổn thất tới 545.000 đô la.

#### Các loại Performance Testing

- **Load test** là cách kiểm tra giúp xác định mức độ tắc nghẽn hiệu suất của ứng dụng trước khi được sử dụng ngoài thực tế.
- **Stress test** là phương pháp kiểm tra giúp xác định giới hạn của ứng dụng về hiệu suất xử lý lưu lượng truy cập thông qua thử nghiệm công việc.
- **Endurance test** có nhiệm vụ đảm bảo cho ứng dụng có thể xử lý tải dự kiến tại một thời điểm cụ thể.
- **Spike test** cho phép kiểm tra phản ứng của phần mềm về các mức tăng đột biến trong quá trình tải.
- **Volume test** là cách kiểm thử hiệu năng của phần mềm về các khối lượng cơ sở dữ liệu khác nhau.
- **Scalability test** được dùng để kiểm tra xem ứng dụng hỗ trợ tăng tải có thể đạt được mức hiệu quả cao hơn không. Đồng thời cũng sẽ lên kế hoạch để cung cấp thêm dung lượng nếu cần thiết.

#### Một số vấn đề phổ biến về Performance Testing

Hiệu suất có thể gặp nhiều vấn đề nhưng phổ biến nhất đó là những vấn đề liên quan đến tốc độ, phản hồi trong bao lâu, tải nhanh hay chậm hoặc có thể mở rộng hay không. Performance Testing có nhiệm vụ điều chỉnh các vấn đề đó và đảm bảo cho các ứng dụng chạy linh hoạt hơn và có nhiều tính năng hơn.

Sau đây là một số vấn đề phổ biến:

- Thời gian tải lâu tức là thời gian bắt đầu khởi chạy của ứng dụng thường bị kéo dài quá một phút.
- Thời gian phản hồi chậm là khi kết thúc thao tác nhập dữ liệu cho đến một lúc lâu sau mới bắt đầu có phản hồi từ ứng dụng.
- Khả năng mở rộng kém tức là khi lượng người dùng tăng lên mà phần mềm không thể xử lý được
- Bottlenecking là hiện tượng lỗi xảy ra làm giảm hiệu năng hoạt động của hệ thống như lỗi mã hoá hay gặp sự cố phần cứng của CPU, bộ nhớ, mạng hoặc hệ điều hành. Giải pháp hiệu quả đó là nhanh chóng tìm kiếm đoạn mã bị lỗi và khắc phục nó.

#### Quy trình Performance Testing

Có nhiều cách được sử dụng để kiểm thử hiệu năng nhằm xác định sự tương thích của ứng dụng đối với các yêu cầu được đặt ra hoặc hỗ trợ so sánh hiệu suất của hai phần mềm khác nhau.

Dưới đây là quy trình về cách thực hiện kiểm thử hiệu năng:

![https://bkhost.vn/wp-content/uploads/2022/06/Quy-trinh-kiem-thu-hieu-nang.jpg](https://bkhost.vn/wp-content/uploads/2022/06/Quy-trinh-kiem-thu-hieu-nang.jpg)

- Xác định các môi trường thử nghiệm như môi trường vật lý, môi trường sản xuất hoặc công cụ kiểm tra nhằm xác định cấu hình phần cứng, phần mềm và mạng được sử dụng bằng bộ testcase. Đồng thời giúp xác định các khó khăn có thể xảy ra trong quá trình kiểm thử hiệu năng.
- Xác định tiêu chí chấp nhận hiệu suất gồm có những yêu cầu về kết quả và sự liên hệ với thông lượng, khả năng phản hồi nhanh chậm và phân bổ tài nguyên của hệ thống. Người kiểm tra cần xác định các tiêu chí và mục tiêu về hiệu suất tối thiểu mà hệ thống cần đạt được.
- Lập kế hoạch và thiết kế các bài kiểm thử hiệu năng nhằm xác định mức độ sử dụng giữa người dùng cuối hoặc một số tình huống giúp kiểm tra các trường hợp có thể sử dụng.
- Cài đặt thử nghiệm bao gồm môi trường, công cụ và một số tài nguyên khác.
- Triển khai thử nghiệm bao gồm các bài kiểm thử hiệu năng theo tiêu chuẩn của test design.
- Thực hiện thử nghiệm và theo dõi các kết quả của quá trình.
- Phân tích, điều chỉnh và kiểm tra lại các kết quả thu thập được.

#### Các chỉ số Performance Testing

Các thông số phổ biến thường được dùng để xử lý trong quá trình kiểm thử hiệu năng gồm có:

- **Bộ nhớ xử lý** để quản lý thời gian thực hiện các nguồn không hoạt động.
- **Bộ nhớ vật lý** có sẵn hỗ trợ cho các quá trình kiểm thử hiệu năng.
- **Sử dụng thời gian đĩa** thực hiện các yêu cầu đọc và ghi.
- **Băng thông** có khả năng hiển thị tần suất số bit trên giây của mạng.
- **Bit riêng tư** được phân bổ cho quy trình cụ thể không thể chia sẻ giữa các quy trình khác có nhiệm vụ đo mức độ rò rỉ và sử dụng bộ nhớ trên hệ thống.
- **Bộ nhớ cam kết** quản lý số lượng bộ nhớ ảo được sử dụng trên hệ thống.
- **Số trang/giây** được ghi và đọc từ đĩa giúp giải quyết các vấn đề lỗi trang cứng.
- **Lỗi trang cứng** xảy ra do xuất hiện các mã không đúng với đĩa.
- **Lỗi trang/giây** là tốc độ xử lý các trang lỗi xảy ra khi quy trình yêu cầu mã từ bên ngoài.
- **CPU** trung bình là số lần phần cứng bị ngắt và bộ xử lý cần tiếp nhận xử lý mỗi giây.
- **Độ dài hàng đợi đĩa** là các yêu cầu được xếp hàng đợi cho đĩa đã chọn trong khoảng thời gian lấy mẫu.
- **Độ dài hàng đợi** đầu ra của mạng giúp xác định độ chậm trễ và tắc nghẽn của quá trình kiểm tra.
- **Tổng số bit mạng** mỗi giây được gửi và nhận trên giao diện.
- **Thời gian phản hồi** được xác định từ khi người dùng nhập yêu cầu đến khi nhận được các ký tự phản hồi đầu tiên.
- **Thông lượng** giúp kiểm tra và đánh giá các máy tính hoặc mạng được yêu cầu mỗi giây.
- **Số lượng kết nối** gộp về các yêu cầu khác nhau được đáp ứng bởi các kết nối giúp cải thiện hiệu suất hoạt động.
- **Số phiên hoạt động** tối đa hoạt động trong một khoảng thời gian cụ thể.
- **Tỷ lệ số lần truy cập** bao gồm [các câu lệnh SQL](https://bkhost.vn/blog/sql-structured-query-language/) được xử lý bởi dữ liệu trong [bộ nhớ đệm](https://bkhost.vn/blog/cache-la-gi/) giúp giải quyết các vấn đề tắc nghẽn.
- **Lượt truy cập trung bình** trên [máy chủ web](https://bkhost.vn/blog/web-server/) trong quá trình thử nghiệm tải.
- **Phân đoạn khôi phục** các dữ liệu mọi lúc, mọi nơi.
- **Khoá cơ sở dữ liệu** cho phép theo dõi và điều chỉnh.
- **Số lần chờ** được xác định theo thời gian có thể được cải thiện khi các dữ liệu được xử lý truy xuất nhanh từ bộ nhớ.
- **Số luồng** cho biết tình trạng chạy và hoạt động của ứng dụng.
- **Thu gom rác** giúp dọn dẹp các dữ liệu không sử dụng và trả lại bộ nhớ cho hệ thống.

#### Ví dụ về Performance Testing của ứng dụng

- Xác định thời gian nếu như có 1000 người dùng cùng lúc vào website thì phải phản hồi lại trong khoảng thời gian dưới 4 giây..
- Xác định thời gian phản hồi của ứng dụng đang tải cả trong tình trạng kết nối yếu.
- Kiểm tra tối đa ứng dụng có thể giải quyết được yêu cầu của bao nhiêu người dùng.
- Kiểm tra nếu có cùng lúc 500 bản ghi thực hiện nhiệm vụ đọc ghi thì cơ sở dữ liệu cần bao nhiêu thời gian thực hiện.
- Giúp xác định mức sử dụng CPU và bộ nhớ của ứng dụng trong điều kiện tải cao điểm.
- Với mỗi lượng tải từ nhẹ đến nặng thì khoảng thời gian ứng dụng phản hồi là bao lâu.

Lưu ý: Một số thuật ngữ như phạm vi chấp nhận được, tải nặng… sẽ được thay thế bằng các con số cụ thể trong quá trình thực nghiệm.

#### Nội dung

k6 là một công cụ kiểm thử hiệu suất mã nguồn mở, tích hợp tốt với Grafana để cung cấp các biểu đồ và báo cáo chi tiết về hiệu suất ứng dụng. k6 cho phép bạn thực hiện kiểm thử tải và stress để đánh giá khả năng mở rộng và độ tin cậy của ứng dụng. Hướng dẫn chi tiết về cách sử dụng k6 cùng với Grafana để thực hiện kiểm thử hiệu suất và phân tích kết quả sẽ được trình bày trong phần này.

1. [Triển khai Performance Testing với Grafana(k6)](11.1-k6)
