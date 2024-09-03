+++
title = "Codacy (Cloud)"
date = "`r Sys.Date()`" 
weight = 2
chapter = false
pre = "<b>7.2 </b>"
+++

Access the Codacy login page: [https://www.codacy.com](https://www.bing.com/ck/a?!&&p=df4d456eb1b9ea7bJmltdHM9MTcyNDI4NDgwMCZpZ3VpZD0xM2ZhODYzZS0yMjYwLTZiMGMtMTlmNy04OTU0MjNhMDZhMTAmaW5zaWQ9NTE5OA&ptn=3&ver=2&hsh=3&fclid=13fa863e-2260-6b0c-19f7-895423a06a10&psq=codacy&u=a1aHR0cHM6Ly93d3cuY29kYWN5LmNvbS8&ntb=1) and choose to log in with GitLab.

![image.png](/images/7-sast/image%2022.png?featherlight=false&width=60pc)

Authorize Codacy.

![image.png](/images/7-sast/image%2023.png?featherlight=false&width=60pc)

This is the interface after authorization.

![image.png](/images/7-sast/image%2024.png?featherlight=false&width=60pc)

Select `add group ecommerce`.

![image.png](/images/7-sast/image%2025.png?featherlight=false&width=60pc)

After adding the group, there will be 2 projects; add them all.

![image.png](/images/7-sast/image%2026.png?featherlight=false&width=60pc)

Once added, we will have 2 projects. Click on `frontend`.

![image.png](/images/7-sast/image%2027.png?featherlight=false&width=60pc)

The `frontend` dashboard displays an overview of the issues.

![image.png](/images/7-sast/image%2028.png?featherlight=false&width=60pc)

In the `Issue` section, detailed security issues are displayed according to severity levels.

![image.png](/images/7-sast/image%2029.png?featherlight=false&width=60pc)

Navigate to `Setting → Integration` and choose `Enable` for all.

![image.png](/images/7-sast/image%2030.png?featherlight=false&width=60pc)

When we create a Merge Request from feature branches to the develop branch and want Codacy to trigger a source code scan, we will enable the `develop` branch.

![image.png](/images/7-sast/image%2031.png?featherlight=false&width=60pc)

In the `frontend` project, we create a new branch.

![image.png](/images/7-sast/image%2032.png?featherlight=false&width=60pc)

In the Edit file, we copy an additional import line.

![image.png](/images/7-sast/image%2033.png?featherlight=false&width=60pc)

We create a Merge Request into the `develop` branch.

![image.png](/images/7-sast/image%2034.png?featherlight=false&width=60pc)

Click on `create merge request`.

![image.png](/images/7-sast/image%2035.png?featherlight=false&width=60pc)

When creating the merge request, a pipeline will be triggered.

![image.png](/images/7-sast/image%2036.png?featherlight=false&width=60pc)

Click on that pipeline to see the details.

![image.png](/images/7-sast/image%2037.png?featherlight=false&width=60pc)

GitLab will redirect to the Codacy website, and Codacy has detected issues.

![image.png](/images/7-sast/image%2038.png?featherlight=false&width=60pc)

You can read more about Codacy here: [https://docs.codacy.com](https://www.bing.com/ck/a?!&&p=6e09d5cfeb919eb7JmltdHM9MTcyNDI4NDgwMCZpZ3VpZD0xM2ZhODYzZS0yMjYwLTZiMGMtMTlmNy04OTU0MjNhMDZhMTAmaW5zaWQ9NTE5OA&ptn=3&ver=2&hsh=3&fclid=13fa863e-2260-6b0c-19f7-895423a06a10&psq=codacy+docs&u=a1aHR0cHM6Ly9kb2NzLmNvZGFjeS5jb20v&ntb=1)
