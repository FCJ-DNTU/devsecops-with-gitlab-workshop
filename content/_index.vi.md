+++
title = "Triển Khai Devsecops trên Gitlab"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
+++

# Triển Khai Devsecops trên Gitlab

#### Tổng quan
Triển khai DevSecOps trên GitLab là một quá trình tích hợp các thực hành bảo mật vào quy trình phát triển phần mềm thông qua việc sử dụng GitLab, một nền tảng DevOps mạnh mẽ và phổ biến. Bài viết này sẽ cung cấp một cái nhìn tổng quan về cách kết hợp bảo mật (Security) vào các giai đoạn phát triển và vận hành phần mềm (Development và Operations) thông qua các công cụ trên GitLab và mã nguồn mở (Open Source).

![images](/images/common/image.png)

Bài lab sẽ hướng dẫn bạn từng bước để thiết lập một quy trình DevSecOps hoàn chỉnh trên nền tảng GitLab. Các kiến thức có được sau bài lab sẽ giúp bạn tích hợp bảo mật vào từng giai đoạn của quy trình phát triển phần mềm, từ quản lý mã nguồn đến triển khai liên tục, giúp đảm bảo hệ thống của bạn an toàn và hiệu quả hơn.

#### Nội dung chính

1. [Tạo tài khoản AWS](1-create-new-aws-account/)
2. [Thiết lập MFA cho tài khoản AWS (Root)](2-mfa-setup-for-aws-user-(root)/)
3. [Tài khoản và Nhóm Admin](3-create-admin-user-and-group/)
4. [Hỗ trợ Xác thực Tài khoản](4-verify-new-account/)