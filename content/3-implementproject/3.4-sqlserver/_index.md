+++
title = "Load Data for SQL Server"
date = "`r Sys.Date()`" 
weight = 4
chapter = false
pre = "<b>3.4 </b>"
+++

In the previous section, we successfully deployed the Frontend and Backend. Now, we will proceed to set up SQL Server.

### 1. Set Up SQL Server

We will use the following command to connect to the SQL database interface in Docker:

```bash
docker exec -it sqlserver /opt/mssql-tools18/bin/sqlcmd -S localhost -U sa -P Str0ngPa5sVvorcl -C
```

![image.png](/images/3-implementproject/3.4-sqlserver/image.png?featherlight=false&width=60pc)

We will run the following command:

```bash
create database ecommerce;
GO
```

![image.png](/images/3-implementproject/3.4-sqlserver/image1.png?featherlight=false&width=60pc)


### 2. Set Up Cloud Beaver

In the previous sections, the Cloud Beaver interface looked like this, and we clicked Next:

Click Next.

![image.png](/images/3-implementproject/3.4-sqlserver/image2.png?featherlight=false&width=60pc)


On the next screen, scroll down, enter the account and password to log in.

![image.png](/images/3-implementproject/3.4-sqlserver/image3.png?featherlight=false&width=60pc)


Click Finish.

![image.png](/images/3-implementproject/3.4-sqlserver/image4.png?featherlight=false&width=60pc)


Proceed to log in again.

![image.png](/images/3-implementproject/3.4-sqlserver/image5.png?featherlight=false&width=60pc)


Click on the logo icon.

![image.png](/images/3-implementproject/3.4-sqlserver/image6.png?featherlight=false&width=60pc)


Click on the plus icon and select New Connection.

![image.png](/images/3-implementproject/3.4-sqlserver/image7.png?featherlight=false&width=60pc)


Since we are working with SQL Server, select the appropriate option.

![image.png](/images/3-implementproject/3.4-sqlserver/image8.png?featherlight=false&width=60pc)


Fill in the information:

- Host: your IPv4 address
- Database: ecommerce
- User name: sa
- User password: Str0ngPa5sVvorcl

![image.png](/images/3-implementproject/3.4-sqlserver/image9.png?featherlight=false&width=60pc)


After filling in the details, click Test to verify the connection.

![image.png](/images/3-implementproject/3.4-sqlserver/image10.png?featherlight=false&width=60pc)


The result shows a successful connection! Click Create to establish the connection.

![image.png](/images/3-implementproject/3.4-sqlserver/image11.png?featherlight=false&width=60pc)


You will see an interface like this. Click on the SQL icon.

![image.png](/images/3-implementproject/3.4-sqlserver/image12.png?featherlight=false&width=60pc)


We will extract the SQL commands from the file `table-init.sql`.

```bash
cat /root/ecommerce-fullstack-netcore-react/table-init.sql
```

![image.png](/images/3-implementproject/3.4-sqlserver/image13.png?featherlight=false&width=60pc)


Copy and paste them into the SQL editor on Cloud Beaver and execute the entire SQL command.

![image.png](/images/3-implementproject/3.4-sqlserver/image14.png?featherlight=false&width=60pc)

The result shows that the table creation was successful.

![image.png](/images/3-implementproject/3.4-sqlserver/image15.png?featherlight=false&width=60pc)

Next, extract the data from the file `data-init.sql`.

```bash
cat /root/ecommerce-fullstack-netcore-react/data-init.sql
```

![image.png](/images/3-implementproject/3.4-sqlserver/image16.png?featherlight=false&width=60pc)


Copy and run the entire file, and the result is successful.

![image.png](/images/3-implementproject/3.4-sqlserver/image17.png?featherlight=false&width=60pc)


Now the data has been loaded into the database. Check the Frontend by reloading it. The products should now be visible.

![image.png](/images/3-implementproject/3.4-sqlserver/image18.png?featherlight=false&width=60pc)


And the Backend returns data for the products.

![image.png](/images/3-implementproject/3.4-sqlserver/image19.png?featherlight=false&width=60pc)


Thus, we have manually deployed the full-stack project!
