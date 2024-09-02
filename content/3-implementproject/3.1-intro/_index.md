+++
title = "Introduction to the Fullstack Project"
date = "`r Sys.Date()`" 
weight = 1
chapter = false
pre = "<b>3.1 </b>"
+++

### 1. Technologies Used:

- ReactJS - Node 18
- .NetCore 6
- SQL Server

### 2. Project Model

![image.png](/images/3-implementproject/3.1-intro/image.png?featherlight=false&width=60pc)

The workflow in the model is as follows:

1. **Frontend (User Interface)**:
    - **React** is used as the framework to build the web interface. React will create interface components for user interaction.
    - When users interact with the interface, React will send requests to the backend via RESTful APIs.
2. **Backend (Server-side Services)**:
    - **.NET 6**, a Microsoft framework, is used to build the backend services.
    - The backend receives requests from the frontend, processes business logic, and performs necessary actions (such as retrieving data from the database or performing calculations).
    - After processing, the backend returns the results to the frontend via RESTful APIs.
3. **Database**:
    - The backend interacts with **SQL Server**, a relational database management system from Microsoft, to store and retrieve data.
    - Data from the database is retrieved by the backend and sent back to the frontend as needed.
