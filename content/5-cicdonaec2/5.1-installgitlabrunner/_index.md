+++
title = "Install GitLab Runner"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>5.1 </b>"
+++

In this tutorial, we will install GitLab Runner to deploy the CI/CD pipeline in the following steps!

You can refer to the installation guide here: [Install GitLab Runner | GitLab](https://docs.gitlab.com/runner/install/)

Let's create a setup.sh file in `/root/tools/gitlab`

```bash
cd /root/tools && vi gitlab/setup.sh
```

Enter the installation commands:

```bash
#!/bin/bash
apt update -y
curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash
apt install gitlab-runner
gitlab-runner --version
```
Run the command:
```bash
sh gitlab/setup.sh
```
Successful installation:

![image.png](/images/5-cicdonaec2/5.1-installgitlabrunner/image.png?featherlight=false&width=60pc)