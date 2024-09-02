+++
title = "Giới Thiệu về Gitlab"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>1.1 </b>"
+++

### 1. GitLab là gì?

Gitlab là một kho lưu trữ Git dựa trên Web cung cấp các kho lưu trữ mở và riêng tư miễn phí, khả năng theo dõi vấn đề. Đây là một nền tảng DevOps toàn diện cho phép các chuyên gia thực hiện tất cả các nhiệm vụ trong một dự án — từ lập kế hoạch dự án và quản lý mã nguồn đến giám sát và bảo mật. Ngoài ra, GitLab còn cho phép các nhóm cộng tác và xây dựng phần mềm tốt hơn.

GitLab giúp các nhóm giảm vòng đời sản phẩm và tăng năng suất, từ đó tạo ra giá trị cho khách hàng. Ứng dụng này không yêu cầu người dùng quản lý quyền cho từng công cụ. Nếu quyền được thiết lập một lần, thì mọi người trong tổ chức đều có quyền truy cập vào mọi thành phần.

### 2. Làm sao để sử dụng Gitlab?

Lợi ích chính của việc sử dụng GitLab là nó cho phép tất cả các thành viên trong nhóm cộng tác trong mọi giai đoạn của dự án. GitLab cung cấp khả năng theo dõi từ lập kế hoạch đến tạo ra sản phẩm, giúp các nhà phát triển tự động hóa toàn bộ vòng đời DevOps và đạt được kết quả tốt nhất có thể. Ngày càng có nhiều nhà phát triển bắt đầu sử dụng GitLab vì tính đa dạng của các tính năng và khả năng sử dụng các khối mã có sẵn.

### 3. Ví dụ về Gitlab

#### 3.1 Bối cảnh dự án

Một công ty phần mềm nhỏ đang phát triển một ứng dụng web phức tạp với một đội ngũ gồm 10 lập trình viên, một quản lý dự án và một chuyên gia bảo mật. Công ty cần một giải pháp để quản lý mã nguồn, tự động hóa quy trình CI/CD, theo dõi tiến độ dự án và đảm bảo an ninh cho ứng dụng.

#### 3.2 Tình huống

1. **Quản lý mã nguồn phân tán:** Mỗi lập trình viên làm việc trên các nhánh riêng của mình và gặp khó khăn trong việc hợp nhất mã nguồn, gây ra xung đột và lỗi.
2. **Thiếu tự động hóa**: Việc kiểm tra mã nguồn và triển khai ứng dụng đều được thực hiện thủ công, dẫn đến nhiều lỗi và mất thời gian.
3. **Khó khăn trong theo dõi tiến độ**: Quản lý dự án gặp khó khăn trong việc theo dõi tiến độ công việc và quản lý các nhiệm vụ.
4. **An ninh không đảm bảo**: Mã nguồn không được kiểm tra thường xuyên về bảo mật, dễ dẫn đến các lỗ hổng.

#### 3.3 Giải pháp với GitLab

**Bước 1: Thiết lập và quản lý mã nguồn**

Nhóm phát triển tạo một dự án trên GitLab và khởi tạo kho lưu trữ Git. Mỗi lập trình viên làm việc trên các nhánh riêng và sử dụng Merge Request (MR) để hợp nhất mã nguồn vào nhánh chính, giúp giảm xung đột và lỗi.

**Bước 2: Tự động hóa CI/CD**

Nhóm phát triển tạo file `.gitlab-ci.yml` để định nghĩa các pipeline tự động hóa quá trình xây dựng, kiểm tra và triển khai ứng dụng.

```yaml
yamlCopy code
stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - echo "Building the application..."
    - # Lệnh build ứng dụng

test:
  stage: test
  script:
    - echo "Running tests..."
    - # Lệnh chạy các bài kiểm tra tự động

deploy:
  stage: deploy
  script:
    - echo "Deploying the application..."
    - # Lệnh triển khai ứng dụng

```

**Bước 3: Quản lý dự án và theo dõi tiến độ**

Quản lý dự án sử dụng các công cụ quản lý dự án của GitLab như bảng Kanban và các issue để theo dõi và quản lý công việc. Mỗi khi có một nhiệm vụ mới, một issue được tạo ra và gán cho lập trình viên tương ứng.

**Bước 4: Đảm bảo an ninh**

Chuyên gia bảo mật sử dụng các công cụ bảo mật của GitLab để kiểm tra mã nguồn tự động và phát hiện các lỗ hổng bảo mật. Các vấn đề bảo mật được phát hiện và giải quyết kịp thời.

#### 3.4 Kết quả

Với GitLab, công ty phần mềm này đã tối ưu hóa quy trình phát triển ứng dụng của mình:

1. **Quản lý mã nguồn hiệu quả**: Giảm thiểu xung đột mã nguồn và lỗi nhờ vào quy trình Merge Request.
2. **Tự động hóa CI/CD**: Giảm thiểu lỗi thủ công và tiết kiệm thời gian với các pipeline tự động.
3. **Theo dõi tiến độ dễ dàng**: Quản lý dự án có cái nhìn tổng quan về tiến độ và dễ dàng quản lý các nhiệm vụ.
4. **Bảo mật tốt hơn**: Mã nguồn được kiểm tra thường xuyên và các lỗ hổng bảo mật được phát hiện kịp thời.

Việc sử dụng GitLab giúp công ty này cải thiện đáng kể hiệu suất làm việc, giảm thời gian phát triển và đảm bảo chất lượng sản phẩm cuối cùng.

### Tham Khảo

[Sử dụng Gitlab để quản lý mã nguồn dự án (notion.site)](https://www.notion.so/899f462b85224829837b3613408ccd54?pvs=21)