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

1. [Giới thiệu về Gitlab](1-introduction)
2. [Các Bước Chuẩn Bị](2-preparation)
3. [Triển khai dự án thủ công](3-implementproject)
4. [Thiết lập dự án trên Gitlab](4-setupgitlab)
5. [Triển khai CI/CD trên cùng một Instance](5-cicdonaec2)
6. [Triển khai CI/CD trên hai Instance](6-cicdon2aec2)
7. [Triển khai SAST](7-sast)
8. [Triển khai SCA](8-sca)
9. [Triển khai Image Scan](9-imagescan)
10. [Triển khai DAST](10-dast)
11. [Triển khai Performance Testing](11-performancetesting)
12. [Dọn dẹp tài nguyên](12-cleanup)