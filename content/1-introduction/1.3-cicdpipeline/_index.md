+++
title = "Introduction to CI/CD Pipeline"
date = "`r Sys.Date()`" 
weight = 3
chapter = false
pre = "<b>1.3 </b>"
+++

### 1. Why CI/CD?

Imagine you are a DevOps engineer at a company. On a bright day, you receive 20 projects that need deployment. You perform all tasks manually, from deploying Frontend, Backend, Database, and Build to Monitoring, not to mention Security steps... It takes you 30 minutes to an hour to complete each deployment. This causes delays in all projects as each project has to wait while others are being built and deployed.

DevOps engineers wonder: "Why don't we create a process to automate Build, Testing, and Deployment based on existing scripts?" And thus, the concept of CI/CD was born.

### 2. What is CI/CD?

**CI/CD** is a combination of **CI (Continuous Integration)** and **CD (Continuous Delivery)** or **Continuous Deployment**, referring to the processes of integrating (integration) frequently and quickly with code and frequently updating new versions (delivery).

![image.png](/images/1-introduction/1.3-cicdpipeline/image.png?featherlight=false&width=90pc)

#### 2.1 Continuous Integration (CI)

Continuous Integration (CI) is a method in software development where developers continuously integrate (merge) new code into a main branch, usually `develop` or `master`. Every time new code is integrated, the CI system automatically builds the project and runs tests to ensure that the new code does not introduce errors or disrupt the current system.

The main goal of CI is to detect errors early in the development process, minimize risks, and ensure that the software is always in a releasable state. A typical CI system includes steps such as:

1. **Commit Code:** Developers frequently commit their code into the version control system (e.g., Git).
2. **Build Automation:** The CI system automatically builds the code after each commit to check for any build errors.
3. **Automated Testing:** After building, the CI system runs automated tests (unit tests, integration tests, etc.) to ensure the new code does not break existing functionality.
4. **Feedback:** If the build or tests fail, the CI system provides immediate feedback to developers so they can fix issues quickly.

#### 2.2 Continuous Delivery (CD)

Continuous Delivery (CD) is a method in software development where code changes (new features, improvements, bug fixes) are automatically built, tested, and prepared for release to production quickly and consistently. CD extends from Continuous Integration (CI) and emphasizes getting the software to a state where it can be released at any time.

The main goal of Continuous Delivery is to ensure that the code is always in a releasable state, meaning the code has been built, tested, and prepared for deployment with just a push of a button.

#### 2.3 Continuous Deployment (CD)

**Continuous Deployment** is an extension of Continuous Delivery (CD), where code changes are automatically deployed to production environments immediately after passing all testing stages. No human intervention is needed; each code change (after passing automated tests) is directly deployed to the live product.

In Continuous Deployment, every commit that passes the testing process is automatically deployed to production. This helps keep the software continuously updated, providing new features, bug fixes, and performance improvements as soon as they are ready.

### 3. Benefits of CI/CD

- Automation of tasks
- Consistency in processes
- Faster releases
- Quality assurance
- Quick feedback
- Rapid error detection

### 4. Key Components of a CI/CD Pipeline

![image.png](/images/1-introduction/1.3-cicdpipeline/image1.png?featherlight=false&width=90pc)

1. **Source Stage:** This stage starts when a developer commits new code to the version control system (e.g., Git). This commit triggers the CI/CD pipeline.
2. **Build Stage:** The source code is compiled and built into application packages or containers. This stage ensures that the code can be built successfully and is ready for the next testing steps.
3. **Test Stage:** The source code is tested by running automated tests like unit tests, integration tests, and end-to-end tests. This stage ensures that the code is error-free and functions as expected.
4. **Deploy Stage:** The tested code is deployed to staging or production environments. Deployment can be automated (continuous deployment) or require human approval (continuous delivery).

Example of a CI/CD Pipeline:

- Developer Commit: A developer commits new code to GitLab.
- Automated Build: The CI/CD system automatically compiles the code and builds it into an application package.
- Automated Testing: The system runs automated tests to ensure the code is free of errors.
- Staging Deployment: The tested code is deployed to a staging environment for final checks.
- Production Deployment: If the code passes all tests, it is deployed to the production environment.
- Monitoring and Feedback: The system monitors and collects feedback from users to improve future releases.

### 5. CI/CD Tools

- GitLab
- Jenkins
- Argo

### 6. GitLab CI/CD

#### 6.1 Components

![image.png](/images/1-introduction/1.3-cicdpipeline/image2.png?featherlight=false&width=90pc)

GitLab CI/CD has two main components:

- Runner: This can be seen as a robot that executes predefined tasks.
- YAML: The script file that the Runner reads and executes.

#### 6.2 GitLab Integrated CI/CD Process

This is a process using GitLab-integrated CI/CD.

![image.png](/images/1-introduction/1.3-cicdpipeline/image3.png?featherlight=false&width=90pc)

- First, developers push code to the `develop` branch, which triggers GitLab Runner to read the `.gitlab-ci.yml` file.
- During the CI phase, GitLab Runner executes steps from preparation, build, to testing as defined in the script.
- We can use Shared Runners (a runner that can handle multiple projects) or Specific Runners (a runner dedicated to a single project), integrating with components like containers, artifacts, etc.
- Finally, the code is deployed to the production environment.

![image.png](/images/1-introduction/1.3-cicdpipeline/image4.png?featherlight=false&width=90pc)

Imagine that on GitLab Runner, there would be a Jenkins Agent as shown below.

![image.png](/images/1-introduction/1.3-cicdpipeline/image5.png?featherlight=false&width=90pc)

### References

[What is CI/CD? Implementing CI/CD with GitLab (topdev.vn)](https://topdev.vn/blog/trien-khai-ci-cd-voi-gitlab/#continuous-integration-ci)