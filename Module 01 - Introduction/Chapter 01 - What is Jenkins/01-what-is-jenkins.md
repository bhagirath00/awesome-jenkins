# What is Jenkins

## Introduction

Imagine you work at a company where 20 developers are all writing code at the same time. Every day, each developer finishes their work and wants to push it to the main codebase. Someone has to manually take that code, run the tests to make sure nothing is broken, package the application, and deploy it to the AWS servers. If even one person makes a mistake in this process, the entire website goes down.

Now multiply that by 20 developers, 5 pushes a day each. That is 100 manual deployment processes every single day. This is not just slow — it is a recipe for disaster.

This is the exact problem **Jenkins** was built to solve.

Jenkins is a free, open-source **automation server**. It watches your code repository 24/7, and every single time a developer pushes new code, Jenkins automatically takes over: it pulls the code, runs all the tests, packages the application, builds the Docker image, pushes it to Amazon ECR, deploys it to your AWS environment, and sends your team a notification on Slack. All of this happens in minutes, without any human clicking a single button.

Think of Jenkins as a tireless, robotic employee who only has one job: **do the same deployment process perfectly, every single time, for every single code change, forever.**

---

## The Core Problem: Integration Hell

Before CI/CD tools like Jenkins existed, software teams worked in long release cycles. Here is how a typical pre-Jenkins workflow looked:

- Developer A works in isolation on a major feature for **3 weeks**.
- Developer B works in isolation on a different feature for **3 weeks**.
- Developer C fixes bugs in isolation for **3 weeks**.
- On release day, all three developers try to merge their work together.
- The code conflicts. A function that Developer A renamed is still being called by Developer B's code. Tests fail everywhere. The production deployment is broken.
- The team now spends **5 days doing nothing but trying to make the code work together** instead of building new features.

This nightmare was so common in the industry that it got a specific name: **"Integration Hell"**.

The solution to Integration Hell is a concept called **Continuous Integration (CI)**:

> **Integrate code changes frequently — ideally, every single time a developer pushes code — and automatically test the integrated result.**

Instead of waiting 3 weeks to find out code is broken, you find out in **5 minutes**. And because the change is small and recent, the fix is simple.

Jenkins is the engine that makes this possible. Every time code is pushed, Jenkins:

1. Pulls the latest code from the Git repository.
2. Merges it with the main branch.
3. Compiles or installs the dependencies.
4. Runs every unit test and integration test.
5. Reports a **Pass** or **Fail** result with a detailed log.

If it fails, the developer who broke it gets notified instantly — while the change is still fresh in their mind.

---

## From CI to the Full CI/CD Pipeline

Once you have CI working (automatic testing on every push), you can extend the automation further into **Continuous Delivery** and **Continuous Deployment**.

| Stage | What It Means | Jenkins Does |
| :--- | :--- | :--- |
| **Continuous Integration (CI)** | Automatically test every code push | Pull code → run tests → report pass/fail |
| **Continuous Delivery (CD)** | Automatically prepare a release-ready build after tests pass | Build Docker image → push to AWS ECR → create deployment package |
| **Continuous Deployment** | Automatically deploy to production after tests pass | Deploy to AWS ECS / EKS → run smoke tests → notify Slack |

The end result is a fully automated path from a developer's local machine to your production AWS environment — with no human intervention required.

Here is what that full pipeline looks like in a real DevOps project:

```
Developer pushes code to GitHub (feature branch)
         │
         ▼
Jenkins detects the push via GitHub Webhook
         │
         ▼
Stage 1 — Checkout: Jenkins clones the repository
         │
         ▼
Stage 2 — Test: Jenkins runs all unit + integration tests (e.g., pytest, mvn test, npm test)
         │
         ├── Tests FAIL → Jenkins marks build as FAILED, sends Slack alert to developer
         │
         ▼
Stage 3 — Build: Jenkins builds the Docker image
         │
         ▼
Stage 4 — Push: Jenkins pushes the image to Amazon ECR
         │
         ▼
Stage 5 — Deploy: Jenkins deploys to Amazon ECS (Fargate) or EKS
         │
         ▼
Stage 6 — Notify: Jenkins sends a "✅ Build #42 deployed successfully" Slack message
```

This entire process takes 5 to 15 minutes, depending on your test suite and build time. Without Jenkins, a human would spend 30 to 60 minutes doing this — and would eventually make a mistake.

---

## The Jenkinsfile — Pipeline as Code

The most powerful idea in Jenkins is that **your pipeline is code**. Instead of clicking through a web interface to configure your CI/CD steps, you write them in a file called a `Jenkinsfile` and commit it to your Git repository alongside your application code.

This has massive benefits:
- Your pipeline is **version controlled** — you can see who changed the pipeline and when.
- Your pipeline is **peer-reviewed** — teammates can review the Jenkinsfile in a pull request before it goes live.
- Your pipeline is **reproducible** — every project uses the same `Jenkinsfile`, ensuring consistent behavior across environments.

Here is a real `Jenkinsfile` for a Java application that deploys to AWS:

```groovy
pipeline {
    // Run on any available agent (any worker machine)
    agent any

    // Environment variables available to all stages
    environment {
        AWS_REGION        = 'us-east-1'
        ECR_REGISTRY      = '123456789012.dkr.ecr.us-east-1.amazonaws.com'
        IMAGE_NAME        = 'my-java-app'
        IMAGE_TAG         = "${BUILD_NUMBER}"   // Jenkins auto-increments this number
        ECS_CLUSTER       = 'production-cluster'
        ECS_SERVICE       = 'my-app-service'
    }

    stages {

        stage('Checkout') {
            steps {
                // Pull the latest code from GitHub
                git branch: 'main',
                    url: 'https://github.com/mycompany/my-java-app.git',
                    credentialsId: 'github-credentials'
            }
        }

        stage('Unit Tests') {
            steps {
                // Run Maven tests and generate a test report
                sh 'mvn clean test'
            }
            post {
                always {
                    // Publish JUnit test results to Jenkins UI
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Build JAR') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                    docker build \
                        -t ${ECR_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG} \
                        -t ${ECR_REGISTRY}/${IMAGE_NAME}:latest \
                        .
                """
            }
        }

        stage('Push to Amazon ECR') {
            steps {
                // Login to ECR, then push both tags
                sh """
                    aws ecr get-login-password --region ${AWS_REGION} | \
                        docker login --username AWS --password-stdin ${ECR_REGISTRY}

                    docker push ${ECR_REGISTRY}/${IMAGE_NAME}:${IMAGE_TAG}
                    docker push ${ECR_REGISTRY}/${IMAGE_NAME}:latest
                """
            }
        }

        stage('Deploy to AWS ECS') {
            steps {
                sh """
                    aws ecs update-service \
                        --cluster ${ECS_CLUSTER} \
                        --service ${ECS_SERVICE} \
                        --force-new-deployment \
                        --region ${AWS_REGION}
                """
            }
        }
    }

    post {
        success {
            slackSend(
                channel: '#deployments',
                color:   'good',
                message: "✅ *${IMAGE_NAME}* Build #${BUILD_NUMBER} deployed to production successfully!"
            )
        }
        failure {
            slackSend(
                channel: '#deployments',
                color:   'danger',
                message: "❌ *${IMAGE_NAME}* Build #${BUILD_NUMBER} FAILED. Check logs: ${BUILD_URL}"
            )
        }
    }
}
```

This single file defines the entire deployment process for your application. It lives at the root of your repository as `Jenkinsfile`. When Jenkins picks it up, the pipeline in the Jenkins web UI is created automatically — no manual configuration needed.

---

## Core Jenkins Terminology

You will hear these terms constantly. Understand them now so nothing confuses you later.

| Term | Plain English Explanation |
| :--- | :--- |
| **Controller (Master)** | The main Jenkins server. It reads your Jenkinsfile and decides what to run and where. |
| **Agent (Node)** | A worker machine where the actual build commands run (tests, Docker builds, etc.). |
| **Job** | Any automated task in Jenkins. A Pipeline Job runs a Jenkinsfile. A Freestyle Job is configured via the UI. |
| **Build** | A single execution of a Job. Build #1, Build #2, etc. |
| **Pipeline** | A series of stages and steps defined in a Jenkinsfile. This is the modern way to use Jenkins. |
| **Stage** | A named phase in the pipeline (e.g., "Test", "Build", "Deploy"). Displayed as columns in the Jenkins UI. |
| **Step** | A single command inside a stage (e.g., `sh 'mvn test'`). |
| **Plugin** | An add-on that gives Jenkins extra capabilities. The Git plugin lets Jenkins talk to GitHub. The Docker plugin lets Jenkins build images. |
| **Build Number** | An auto-incrementing number for each run. Used to tag Docker images so you can trace exactly which build a deployed image came from. |
| **Workspace** | A directory on the agent where Jenkins puts the code and files for each build. |
| **Artifact** | A file produced by a build (e.g., a `.jar` file, a Docker image, a `.zip` archive). |

---

## Why Jenkins Is Still the Most Used CI/CD Tool

Jenkins was created in 2011, and in 2024, it is still the most widely deployed CI/CD server on the planet. Here is why it has not been replaced:

1. **It is Self-Hosted**: You run Jenkins on your own infrastructure. Your code never leaves your network. This is critical for banks, healthcare companies, and government projects where data cannot go to external SaaS platforms.

2. **Unmatched Plugin Ecosystem**: Over 1,800 community-maintained plugins. If a tool exists in DevOps, there is almost certainly a Jenkins plugin for it.

3. **Total Control**: You control the Jenkins version, the plugins, the agent machines, the networking, and the security. Nothing is locked down by a vendor.

4. **Works with Everything**: Jenkins does not care what language your application is written in, what cloud you use, or what deployment target you have. It runs shell commands — and shell commands can do anything.

5. **Mature and Battle-Tested**: Running for over a decade in some of the largest companies in the world (Netflix, LinkedIn, eBay). Edge cases and failure modes are well-documented.

---

## Summary

Jenkins is a free, open-source automation server that sits at the heart of the CI/CD pipeline in modern DevOps. It solves the "integration hell" problem by automatically testing, building, and deploying code every time a developer pushes a change.

The most important things to remember from this file:

- Jenkins automates the entire path from a code push to a production deployment on AWS.
- Your pipeline is defined in a `Jenkinsfile` that lives in your Git repository — this is called **Pipeline as Code**.
- Jenkins is built around stages (Test, Build, Deploy) and steps (individual shell commands inside a stage).
- It has over 1,800 plugins, letting it integrate with any tool in your DevOps stack — GitHub, Docker, AWS ECR, ECS, Kubernetes, Slack, and more.
- Jenkins is self-hosted, giving you complete control over your pipeline and your data.

In the next file, `02-jenkins-vs-other-cicd-tools.md`, you will compare Jenkins against modern alternatives like GitHub Actions, GitLab CI, and CircleCI — learning exactly when each tool is the right choice so you can make confident architectural decisions.
