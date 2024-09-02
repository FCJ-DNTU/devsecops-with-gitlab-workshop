+++
title = "Create project and push source code to Github"
date = "`r Sys.Date()`" 
weight = 2
chapter = false
pre = "<b>4.2 </b>"
+++

### 1. Creating a Project

We already have a group named `ecommerce` and a project called `frontend` from the previous section.

If you haven't created them yet, please proceed to create the `ecommerce` group and a new project named `frontend` within that group.

In the `ecommerce` group, select "Create new project".

![image.png](/images/4-setupgitlab/4.2-pushcode/image.png?featherlight=false&width=60pc)

In "Create new project," choose "Create blank project".

![image.png](/images/4-setupgitlab/4.2-pushcode/image1.png?featherlight=false&width=60pc)

Fill out the required information and click "Create Project".

![image.png](/images/4-setupgitlab/4.2-pushcode/image2.png?featherlight=false&width=60pc)

We now have the `frontend` project.

![image.png](/images/4-setupgitlab/4.2-pushcode/image3.png?featherlight=false&width=60pc)

Next, create a new project named `backend` in the `ecommerce` group using the same steps as above.

![image.png](/images/4-setupgitlab/4.2-pushcode/image4.png?featherlight=false&width=60pc)

### 2. Pushing Code to GitLab

First, let's check if Git is installed on your machine.

```bash
git --version
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image5.png?featherlight=false&width=60pc)

Next, create a `projects` directory for work.

```bash
mkdir /root/projects && cd /root/projects
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image6.png?featherlight=false&width=60pc)

Proceed to clone the repository as instructed in the GitLab console.

![image.png](/images/4-setupgitlab/4.2-pushcode/image7.png?featherlight=false&width=60pc)

Then create a new directory and navigate into it:

```bash
mkdir -p ecommerce/ && cd ecommerce/
```

When you access the `frontend` project on GitLab, you will see the setup instructions.

![image.png](/images/4-setupgitlab/4.2-pushcode/image27.png?featherlight=false&width=60pc)

Configure your global settings accordingly.

![image.png](/images/4-setupgitlab/4.2-pushcode/image8.png?featherlight=false&width=60pc)

Next, clone the `frontend` project. When cloning, you'll need your email and password.

![image.png](/images/4-setupgitlab/4.2-pushcode/image9.png?featherlight=false&width=60pc)

{{% notice info %}}
If you log in using a Google or GitHub account, you should set a password.
![image.png](/images/4-setupgitlab/4.2-pushcode/image10.png?featherlight=false&width=60pc)
{{% /notice %}}

After cloning, copy all files from the `react-ecommerce-template` folder to the newly cloned `frontend` folder.

```bash
cd .. 
unzip /home/ubuntu/ecommerce-fullstack-netcore-react.zip 
cp -rf ecommerce-fullstack-netcore-react/frontend/* frontend/
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image11.png?featherlight=false&width=60pc)

In `/projects/frontend`, push the code to GitLab as follows:

```bash
git checkout -b main
git add .
git commit -m "config(project): Initial frontend project"
git push -f origin main
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image12.png?featherlight=false&width=60pc)

After pushing the code, you will see the files in the `frontend` project.

![image.png](/images/4-setupgitlab/4.2-pushcode/image13.png?featherlight=false&width=60pc)

For the `backend` project, follow similar steps:

```bash
cd /root/projects
git clone your_backend_project
cp -rf ecommerce-fullstack-netcore-react/backend/* backend/
cd backend
git checkout -b main
git add .
git commit -m "config(project): Initial backend project"
git push -f origin main
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image14.png?featherlight=false&width=60pc)

The source code will now be available in the `backend` project.

![image.png](/images/4-setupgitlab/4.2-pushcode/image15.png?featherlight=false&width=60pc)

{{% notice info %}}
🡇 Content 🡇
{{% /notice %}}

Each time you clone, you will need to log in when pushing or cloning code, which is time-consuming and not secure. Therefore, you can set up an SSH key for the project:

Create a new SSH key.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Press Enter to accept the defaults, or you can fill in all the details.

![image.png](/images/4-setupgitlab/4.2-pushcode/image16.png?featherlight=false&width=60pc)

Start the SSH agent.

```bash
eval $(ssh-agent -s)
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image17.png?featherlight=false&width=60pc)

Add the SSH key to the SSH agent.

```bash
ssh-add ~/.ssh/id_rsa
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image18.png?featherlight=false&width=60pc)

Check the SSH keys added.

```bash
ssh-add -l
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image19.png?featherlight=false&width=60pc)

Copy the key using the following command.

```bash
cat ~/.ssh/id_rsa.pub
```

![image.png](/images/4-setupgitlab/4.2-pushcode/image20.png?featherlight=false&width=60pc)

Go to User Settings and add a new SSH Key.

![image.png](/images/4-setupgitlab/4.2-pushcode/image21.png?featherlight=false&width=60pc)

Test with the backend project by creating a **README.md** file.

![image.png](/images/4-setupgitlab/4.2-pushcode/image22.png?featherlight=false&width=60pc)

Add a new line.

![image.png](/images/4-setupgitlab/4.2-pushcode/image23.png?featherlight=false&width=60pc)

You should change the remote from HTTPS to SSH. You can get the SSH remote URL from the project.

![image.png](/images/4-setupgitlab/4.2-pushcode/image24.png?featherlight=false&width=60pc)

Change the remote URL.

```bash
git remote set-url origin git@gitlab.com:ecommerce2100134/backend.git
```

Push the code.

```bash
git add .
git commit -m "update README.md file"
git push -f origin main
```

As a result, you no longer need a password to push code.

![image.png](/images/4-setupgitlab/4.2-pushcode/image25.png?featherlight=false&width=60pc)

Finally, the README.md file has been successfully updated!

![image.png](/images/4-setupgitlab/4.2-pushcode/image26.png?featherlight=false&width=60pc)

Reference: [Use SSH keys to communicate with GitLab | GitLab](https://docs.gitlab.com/ee/user/ssh.html)

{{% notice info %}}
🡅 Content 🡅
{{% /notice %}}