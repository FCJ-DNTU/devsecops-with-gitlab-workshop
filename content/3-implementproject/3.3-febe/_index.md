+++
title = "Deploying Frontend and Backend"
date = "`r Sys.Date()`" 
weight = 3
chapter = false
pre = "<b>3.3 </b>"
+++

In the previous section, we uploaded the files to the VSCode SSH server under the Ubuntu account and unzipped the file into a directory in `/root`.

![image.png](/images/3-implementproject/3.3-febe/image.png?featherlight=false&width=60pc)

We will now proceed to deploy the Frontend and Backend.

### 1. Deploying the Backend

Navigate to the `ecommerce-fullstack-netcore-react/backend/` directory and run:

```bash
dotnet restore
```

This command is used to install the required dependencies.

![image.png](/images/3-implementproject/3.3-febe/image1.png?featherlight=false&width=60pc)

Then, start the Backend using the following command:

```bash
dotnet run
```

The CLI will indicate that the Backend is running on port 5214.

![image.png](/images/3-implementproject/3.3-febe/image2.png?featherlight=false&width=60pc)
Access port 5214/swagger in your browser to check the running results and confirm that the Backend is running successfully!

![image.png](/images/3-implementproject/3.3-febe/image3.png?featherlight=false&width=60pc)

Test a few functionalities.

![image.png](/images/3-implementproject/3.3-febe/image4.png?featherlight=false&width=60pc)

We encountered an error due to the database not being set up. You will work on this in the next section!

When using the command above, if we exit using Ctrl + C, the Backend will stop running.

![image.png](/images/3-implementproject/3.3-febe/image5.png?featherlight=false&width=60pc)

We will proceed to run the Backend in the background by using:

```bash
nohup dotnet run > run_bg.txt 2>&1 &
```

![image.png](/images/3-implementproject/3.3-febe/image6.png?featherlight=false&width=60pc)

And the result is that Swagger continues to run normally!

![image.png](/images/3-implementproject/3.3-febe/image7.png?featherlight=false&width=60pc)

### 2. Deploying the Frontend

Navigate to the `frontend` directory:

```bash
cd /root/ecommerce-fullstack-netcore-react/frontend/
```

![image.png](/images/3-implementproject/3.3-febe/image17.png?featherlight=false&width=60pc)

Install the libraries using the following command:

```bash
npm install
```

{{% notice info %}}
🡇 Content 🡇
{{% /notice %}}
If you encounter issues running `npm i`, 

![image.png](/images/3-implementproject/3.3-febe/image8.png?featherlight=false&width=60pc)

You need to run the following command:

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

And the issue was successfully resolved!

![image.png](/images/3-implementproject/3.3-febe/image9.png?featherlight=false&width=60pc)
{{% notice info %}}
🡅 Content 🡅
{{% /notice %}}

Proceed to start the Frontend project with:

```bash
npm start
```

![image.png](/images/3-implementproject/3.3-febe/image10.png?featherlight=false&width=60pc)

After starting, check port 3000 in your browser to verify that the website has started successfully!

![image.png](/images/3-implementproject/3.3-febe/image11.png?featherlight=false&width=60pc)

![image.png](/images/3-implementproject/3.3-febe/image12.png?featherlight=false&width=60pc)

However, like the Backend, there is an issue where the website will immediately stop if we use Ctrl + C. The solution is to run it in the background. At this step, you can use `nohup` as mentioned above.

Another approach we can use is `pm2`.

Proceed to install `pm2`. You can refer to the installation link here: [PM2 - Quick Start (keymetrics.io)](https://pm2.keymetrics.io/docs/usage/quick-start/)

```bash
npm install pm2@latest -g
```

![image.png](/images/3-implementproject/3.3-febe/image13.png?featherlight=false&width=60pc)

Then, start the FE project with `pm2`:

```bash
pm2 start npm --name "ecommerce" -- run "start"
```

![image.png](/images/3-implementproject/3.3-febe/image14.png?featherlight=false&width=60pc)

The result is that the website continues to run normally. Below, as we haven't set up SQL, the products are not displayed.

![image.png](/images/3-implementproject/3.3-febe/image15.png?featherlight=false&width=60pc)

Additionally, `pm2` has monitoring functionality:

```bash
pm2 monit
```

![image.png](/images/3-implementproject/3.3-febe/image16.png?featherlight=false&width=60pc)

Thus, we have successfully deployed the Frontend and Backend.
