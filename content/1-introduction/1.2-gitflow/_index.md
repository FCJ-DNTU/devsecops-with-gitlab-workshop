+++
title = "Introduction to Gitflow"
date = "`r Sys.Date()`" 
weight = 2
chapter = false
pre = "<b>1.2 </b>"
+++

### 1. The Problem

In our daily work, we frequently interact with Git, so everyone is likely familiar with Git's concepts of branches and commands like checkout, merge, and revert...

When developing a new feature, we typically checkout from the current branch to a new branch to work on it. In a team environment, multiple developers may work on various features, each on their own branch, developing independently and in parallel. Everything would be perfect if, on a sunny day, merging these branches back together happened without any conflicts.

Gitflow was created to define principles for branching and feature development, as well as product releases. It is a workflow designed to be as convenient as possible for developers using Git.

### 2. What is Gitflow?

Gitflow is a concept that outlines how to branch and coordinate development, feature releases, and product releases using Git.

> *Gitflow is adapted by different development teams based on their environment and conditions, with managers setting rules for the development workflow. It can be customized or based on an existing flow.*

There are many Gitflow models introduced, but the most famous one is Vincent Driessen's branching model.

### 3. Gitflow According to Vincent Driessen's Branching Model

![image.png](/images/1-introduction/1.2-gitflow/image.png)

Vincent Driessen's Gitflow model is essentially as follows:

- The starting branch is the master (or main) branch. This branch should not have any changes made to it except for deploying new tags.
- At the beginning, we checkout from the master branch to the develop branch. This branch will aggregate code from the feature/* branches.
- Each feature is then checked out from develop into branches with the prefix feature. (For example, the login feature might be on feature/login-ui, and the logout feature on feature/logout-ui...) and developed independently. Once completed, the feature branches are merged into develop.
- At each release stage, we merge the code from the develop branch into the release branch. The release branch serves as the staging environment for the project. This branch is the closest to the Production environment. The release branch will include bug fixes but no new feature commits. If bugs arise in the release branch, fix them in the release branch and merge the code back into develop, continuing to develop new features.
- Once all bugs in the release branch are resolved, we merge the code into the master branch. At the master branch, we can tag the commit to mark the release version. This helps in code backup as it allows us to revert to older versions. It also facilitates traceability and code tracking.
- When running production, issues may arise, and we will checkout from master into a branch with the prefix hotfix/ (for example, if we need to fix a bug in the shopping cart, we would name it hotfix/1-shopping-cart, noting that 1 is the Issue ID we created and labeled as a bug).
- When code from master is merged from the release branch, the webhook of the source code management app (GitHub, GitLab, Aruze DevOps, etc.) will trigger events and activate the CI/CD pipeline to deploy production (e.g., deploy database → deploy backend → deploy frontend, followed by monitoring and logging during the product's runtime). This task will be handled by DevOps Engineers.

### References

[Using GitLab to Manage Project Source Code (notion.site)](https://www.notion.so/899f462b85224829837b3613408ccd54?pvs=21)
