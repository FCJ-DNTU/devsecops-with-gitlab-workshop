+++
title = "Implementing Performance Testing"
date = "`r Sys.Date()`" 
weight = 11
chapter = false
pre = "<b>11. </b>"
+++

#### Content

Performance Testing is the process of evaluating how an application performs under various load conditions. The goal is to ensure that the application can handle the expected workload and meet performance requirements in a production environment.

**Performance Testing is a phase in software testing. This phase is used to assess various aspects of the software**, including:

- Data load speed.
- The time it takes for the software to respond to requests.
- Stability.
- Reliability.
- The ability to utilize resources to improve actual performance.

Performance Testing focuses on three main areas:

- Determining whether the application responds quickly.
- Determining the maximum number of users the software can handle.
- Assessing the software's stability under different load conditions.

# **Why Perform Performance Testing?**

A software system's operation relies not only on available features but also on the application's performance. Performance testing plays a crucial role in detecting and resolving performance issues accurately.

Performance testing identifies how well the application features meet work requirements and ensures the accuracy of application uptime in real-world scenarios.

Some examples of the importance of performance testing:

- Among 500 Fortune companies, 56% experience downtime with an average of 1.6 hours per week, leading to losses of up to $896,000 per week and more than $46 million per year.
- Google.com suffered a loss of $545,000 due to a 5-minute downtime on August 19, 2013.

# **Types of Performance Testing**

- **Load Testing** helps identify performance bottlenecks before the application is used in the real world.
- **Stress Testing** determines the application's limits regarding handling traffic through workload experiments.
- **Endurance Testing** ensures that the application can handle the expected load over a specific period.
- **Spike Testing** evaluates how the software responds to sudden increases in load.
- **Volume Testing** assesses the performance of the software with varying database sizes.
- **Scalability Testing** checks whether the application can efficiently handle increased load and plans for additional capacity if necessary.

# **Common Issues in Performance Testing**

Performance issues can vary but commonly include problems related to speed, response time, load handling, or scalability. Performance Testing aims to address these issues and ensure applications run smoothly with more features.

Common issues include:

- Long load times, where the application's startup time exceeds one minute.
- Slow response times, where there is a significant delay from the end of data entry to receiving a response from the application.
- Poor scalability, where the software cannot handle an increasing number of users.
- Bottlenecking, which occurs when errors degrade system performance due to faulty code or hardware issues such as CPU, memory, network, or operating system problems. The effective solution is to quickly identify and fix the faulty code.

# **Performance Testing Process**

There are various methods used for performance testing to determine application compatibility with requirements or to compare the performance of different software.

Here is the process for conducting performance testing:

![Performance Testing Process](https://bkhost.vn/wp-content/uploads/2022/06/Quy-trinh-kiem-thu-hieu-nang.jpg)

- Identify test environments such as physical environments, production environments, or testing tools to determine the hardware, software, and network configurations used by test cases. This also helps identify potential challenges during performance testing.
- Define performance acceptance criteria, including requirements for results and relationships with throughput, response times, and system resource allocation. Testers need to establish performance criteria and goals that the system must achieve.
- Plan and design performance tests to determine usage levels between end users or scenarios to test various cases.
- Set up the test environment, including tools and other resources.
- Execute the tests according to the test design standards.
- Monitor and analyze the results of the testing process.
- Analyze, adjust, and re-test the collected results.

# **Performance Testing Metrics**

Common metrics used in performance testing include:

- **Processing Memory** to manage the execution time of inactive resources.
- **Physical Memory** available for performance testing processes.
- **Disk Utilization** for performing read and write operations.
- **Bandwidth** showing the frequency of bits per second on the network.
- **Private Bytes** allocated to specific processes that cannot be shared between other processes, used to measure memory leaks and usage.
- **Committed Memory** managing the amount of virtual memory used by the system.
- **Pages/Second** written and read from disk to address hard page faults.
- **Hard Page Faults** occurring due to incorrect disk codes.
- **Page Faults/Second** is the rate at which page faults occur when the process requests code from outside.
- **CPU** average is the number of hardware interrupts and processing required per second.
- **Disk Queue Length** for requests queued for the selected disk during the sample period.
- **Queue Length** for network output to determine delays and bottlenecks in the testing process.
- **Total Network Bits** sent and received per second on the interface.
- **Response Time** from the moment a user makes a request until the first character of the response is received.
- **Throughput** for testing and evaluating the required computing or network requests per second.
- **Number of Connections** aggregated from different requests met by connections to improve performance.
- **Session Count** for the maximum number of concurrent sessions within a specific timeframe.
- **Hit Rate** including [SQL statements](https://bkhost.vn/blog/sql-structured-query-language/) processed by data in [cache](https://bkhost.vn/blog/cache-la-gi/) to address bottleneck issues.
- **Average Hits** on [web servers](https://bkhost.vn/blog/web-server/) during load testing.
- **Recovery Segments** for data recovery at all times.
- **Database Locks** for monitoring and adjustment.
- **Wait Counts** determined by the time that can be improved as data is processed quickly from memory.
- **Thread Count** for tracking the application's running and operational status.
- **Garbage Collection** for cleaning up unused data and freeing memory for the system.

# **Examples of Performance Testing for Applications**

- Determine the response time if 1000 users access the website simultaneously, which should be under 4 seconds.
- Assess the application's response time under both strong and weak connection conditions.
- Test the maximum number of users the application can handle.
- Evaluate how long the database takes to perform read and write operations with 500 concurrent records.
- Determine the CPU and memory usage of the application under peak load conditions.
- Measure the application's response time for different load levels, from light to heavy.

Note: Terms such as acceptable range, heavy load, etc., will be replaced with specific numbers during experimentation.

k6 is an open-source performance testing tool that integrates well with Grafana to provide detailed charts and reports on application performance. k6 allows you to perform load and stress testing to assess the scalability and reliability of the application. A detailed guide on how to use k6 along with Grafana for performance testing and result analysis will be presented in this section.

1. [Implementing Performance Testing with Grafana (k6)](11.1-k6)
