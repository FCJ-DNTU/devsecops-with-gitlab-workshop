+++
title = "Triển khai Gitflow cơ bản trên Gitlab"
date = "`r Sys.Date()`" 
weight = 3
chapter = false
pre = "<b>4.3 </b>"
+++


Bài này chúng ta sẽ triển khai Gitflow cơ bản trên Gitlab. Nếu bạn chưa xem hoặc chưa nắm rõ luồng hoạt động thì hãy quay lại [1.2 Giới thiệu Gitflow](1-introduction/1.2-gitflow)

Chúng ta đã có code của frontend từ mục trước. Đến đây chúng ta sẽ triển khai một gitflow 
**đơn giản**.

![image.png](/images/4-setupgitlab/4.3-gitflow/image.png?featherlight=false&width=60pc)

Trước khi bắt đầu phát triển một tính năng mới chúng ta nên tạo 1 Issue 

![image.png](/images/4-setupgitlab/4.3-gitflow/image1.png?featherlight=false&width=60pc)

Trong New Issue

- Title: “name - title”
- Type: Issue
- Description: update new readme file for frontend project
- Assignee: Assignee to me
- Labels: feature

![image.png](/images/4-setupgitlab/4.3-gitflow/image2.png?featherlight=false&width=60pc)

Bây giờ chúng ta sẽ áp dụng mô hình trên vào project. đầu tiên chúng sẽ tạo một nhánh mới

![image.png](/images/4-setupgitlab/4.3-gitflow/image3.png?featherlight=false&width=60pc)

Đặt tên nhánh mới là develop từ nhánh main.

![image.png](/images/4-setupgitlab/4.3-gitflow/image15.png?featherlight=false&width=40pc)

Sau đó chúng ta sẽ tạo thêm nhánh feature/edit_readme từ nhánh develop. Chúng ta giả lập việc mà chúng ta phát triển một tính năng cụ thể. 

![image.png](/images/4-setupgitlab/4.3-gitflow/image16.png?featherlight=false&width=40pc)

Bấm vào file README.md tại nhánh feature/edit_readme

![image.png](/images/4-setupgitlab/4.3-gitflow/image4.png?featherlight=false&width=60pc)

Chọn Edit → Edit Single File 

![image.png](/images/4-setupgitlab/4.3-gitflow/image5.png?featherlight=false&width=60pc)

Ở đây là edit_readme chúng ta tiến hành sửa file readme trong nhánh feature/edit_readme và commit changes

```markdown
Ecommerce - Getting Started
============================

Welcome to the Ecommerce project! This repository contains the source code for an online shoe store built using React. This guide will help you get started with setting up, running, and deploying the project.

Prerequisites
-------------

Before you start, ensure you have the following installed on your machine:

*   [Node.js](https://nodejs.org/) (v12.x or later)
*   [npm](https://www.npmjs.com/) (v6.x or later)

Project Setup
-------------

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

### Available Scripts

In the project directory, you can run the following scripts:

#### `npm start`

*   Launches the app in development mode.
*   Open [http://3.106.228.72:3000](http://3.106.228.72:3000) to view it in the browser.
*   The page will automatically reload if you make edits.
*   Any lint errors will be displayed in the console.

#### `npm test`

*   Starts the test runner in interactive watch mode.
*   Refer to the running tests section for more details.

#### `npm run build`

*   Builds the app for production, outputting the result to the `build` folder.
*   React is bundled in production mode and optimized for the best performance.
*   The build is minified, and filenames include hashes for efficient caching.
*   Your app will be ready for deployment. See the [deployment guide](https://facebook.github.io/create-react-app/docs/deployment) for more information.

#### `npm run eject`

*   **Note:** This is a one-way operation. Once you eject, you cannot revert this action.
*   Ejecting allows you to take full control of the build configurations (webpack, Babel, ESLint, etc.).
*   You should only use eject if you need more customization than what Create React App provides.

Learn More
----------

*   Create React App Documentation - Detailed documentation for using and configuring Create React App.
*   React Documentation - Comprehensive guide to learning and using React.

### Additional Resources

*   **Code Splitting:** [Guide](https://facebook.github.io/create-react-app/docs/code-splitting) on how to split your code into smaller bundles.
*   **Bundle Size Analysis:** [Guide](https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size) for analyzing the size of your bundles.
*   **Progressive Web App (PWA):** [Guide](https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app) on making your app a PWA.
*   **Advanced Configuration:** [Guide](https://facebook.github.io/create-react-app/docs/advanced-configuration) for advanced customization.
*   **Deployment:** [Guide](https://facebook.github.io/create-react-app/docs/deployment) on how to deploy your app to different hosting platforms.

### Troubleshooting

*   **Build Fails to Minify:** [Guide](https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify) to troubleshoot issues with the build process.
```

![image.png](/images/4-setupgitlab/4.3-gitflow/image6.png?featherlight=false&width=60pc)

Bạn nên để ý #1 đây là id của Issue mà chúng ta đã tạo trước đó. Ngay khi đó thì bên Issue sẽ có hoạt động của commit đó 

![image.png](/images/4-setupgitlab/4.3-gitflow/image7.png?featherlight=false&width=60pc)

Sau khi phát triển xong tính năng mới cụ thể ở đây là thay đổi file readme trong nhánh feature/edit_readme. Chúng ta se merge code về nhánh develop.

Chúng ta sẽ tạo mới 1 Merge Request.

![image.png](/images/4-setupgitlab/4.3-gitflow/image8.png?featherlight=false&width=60pc)

Chọn source branch là  feature/edit_readme target branch là develop. Sau đó nhấn Compare branchs and continue.

![image.png](/images/4-setupgitlab/4.3-gitflow/image9.png?featherlight=false&width=60pc)

Tại giao diện New merge request bạn có thể đặt Title (Tiêu đề), Description (mô tả), Assingee (chỉ đỉnh thực hiện), Reviewer (người Review)

![image.png](/images/4-setupgitlab/4.3-gitflow/image10.png?featherlight=false&width=60pc)

Kéo xuống dưới thêm label feature → nhấn enable “Delete source branch when merge request is accepted.” → create merge request. 

![image.png](/images/4-setupgitlab/4.3-gitflow/image11.png?featherlight=false&width=60pc)

Tại giao diện merge request nhấn Merge.

![image.png](/images/4-setupgitlab/4.3-gitflow/image12.png?featherlight=false&width=60pc)

Merge thành công! bây giờ file readme bên develop đã thay đổi. 

![image.png](/images/4-setupgitlab/4.3-gitflow/image13.png?featherlight=false&width=60pc)

Tại Issue sau khi chúng ta đã merge về nhánh develop thì chúng ta sẽ Close Issue

![image.png](/images/4-setupgitlab/4.3-gitflow/image14.png?featherlight=false&width=60pc)

Chúng ta muốn tiếp tục thực hiện tính năng mới thì tạo mới một issue, một branch từ develop rồi tiếp tục vòng đời như vậy.

Đối với project backend thì bạn cũng sẽ làm tương tự như vậy!

Nếu bạn tò mò về các bước nâng cao hơn thì hãy tham khảo link sau đây nhé: 

[https://youtu.be/vQgcl8VouLU](https://youtu.be/vQgcl8VouLU)