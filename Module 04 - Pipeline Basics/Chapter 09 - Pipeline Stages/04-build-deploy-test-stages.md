# The Build-Test-Deploy Workflow: A Real-World Roadmap

## 1. Introduction: The Professional's Standard

Imagine you are a world-class automotive engineer who has been hired
to design the most advanced sports car in history.
You don't just "Build" the car and then immediately sell it to a
customer.
If you did, the first time they hit 100 miles per hour, the wheels
might fall off.

Instead, you follow a **Rigorous, Step-by-Step Process**.
First, you "Build" the components in a controlled laboratory.
Second, you "Test" the car on a specialized track, pushing it to
its absolute limits to see where it breaks.
Third, you "Deploy" the car to the showroom only after it has
passed every single quality check.
Each of these steps is a distinct "Phase" of the car's birth.

In the world of Jenkins and DevOps, this is known as the
**Build-Test-Deploy Lifecycle**.
It is the standard roadmap for almost every software project in
existence.
While we have looked at the "Syntax" of stages in the previous files,
in this file, we are going to look at the "Strategy."
We will build a complete, production-grade Jenkinsfile that models
this lifecycle, and we will look at exactly what happens in each
phase when you are deploying to a modern AWS cloud environment.

In this file, we are going to go into **unprecedented technical depth**
on the most common roadmap in the industry.
We will look at the tools used in each stage, the common failures
you will face, and how to build a "Seamless Flow" that takes code
from a developer's brain to a customer's browser in minutes.

---

## 2. Stage 1: The Build (The "Ingredients" Phase)

The goal of the Build stage is to take the "Raw Source Code" (the text)
and turn it into a "Runnable Artifact" (the product).

### Key Actions in this Stage:
- **Dependency Resolution:** Downloading libraries (like the AWS SDK).
- **Compilation:** Turning Java or TypeScript into machine code.
- **Packaging:** Creating a `.jar`, `.war`, or `.zip` file.
- **Dockerization:** Creating a container image for AWS EKS.

### The Senior Mindset:
In this stage, "Speed is King."
You want to optimize your build tool (like Maven or NPM) to use
caching so that you don't download the whole internet every time.
If your Build stage takes 20 minutes, your developers will be
bored and frustrated.
If it takes 2 minutes, they will be productive and happy.

---

## 3. Stage 2: The Test (The "Quality Gate" Phase)

The goal of the Test stage is to find "Bugs" before your customers do.
A senior DevOps engineer views this stage as a **Security Guard**.
If the code is "Dirty," the guard doesn't let it pass to the next stage.

### Types of Tests in a Professional Pipeline:
1.  **Unit Tests:** Testing individual functions in isolation.
2.  **Integration Tests:** Testing how different parts of the app
    talk to each other (and to AWS services like S3).
3.  **Static Analysis (Linting):** Checking the "Style" and "Quality"
    of the code without actually running it.
4.  **Security Scans:** Looking for known vulnerabilities in your
    external libraries.

---

## 4. Stage 3: The Deploy (The "Launch" Phase)

The goal of the Deploy stage is to take the "Proven Product" and
place it where the customers can use it.

### Practical AWS Deployment Patterns:
- **Blue/Green Deployment:** You create a brand new version of your
  app next to the old one, and you only switch the traffic when you
  are 100% sure the new one is working.
- **Canary Deployment:** You only send 5% of your customers to the
  new version to see if they have any problems.
- **S3 Upload:** For a simple website, you just sync your files to
  an Amazon S3 bucket behind a CloudFront CDN.

---

## 5. The Complete Real-World Jenkinsfile

Here is a full, professional blueprint that incorporates everything
we have learned in this module.

```groovy
pipeline {
    agent any
    
    options {
        // Safety first: Stop if it takes longer than 30 mins
        timeout(time: 30, unit: 'MINUTES')
        // Add timestamps for debugging
        timestamps()
    }
    
    stages {
        stage('Source: Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build: Compile') {
            steps {
                echo 'Building the application...'
                // Using Maven to build a Java JAR
                sh 'mvn clean package -DskipTests'
            }
        }
        
        stage('Test: Quality Gate') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        sh 'mvn test'
                    }
                }
                stage('Linting') {
                    steps {
                        echo 'Checking code style...'
                        sh './run-linter.sh'
                    }
                }
            }
        }
        
        stage('Deploy: AWS Staging') {
            when {
                branch 'develop'
            }
            steps {
                echo 'Deploying to the Staging Environment on AWS...'
                sh './deploy-aws-staging.sh'
            }
        }
        
        stage('Deploy: AWS Production') {
            when {
                branch 'main'
            }
            steps {
                echo 'Deploying to the LIVE Production Environment...'
                sh './deploy-aws-prod.sh'
            }
        }
    }
}
```

---

## Summary

- The Build-Test-Deploy lifecycle is the industry standard roadmap for CI/CD
- The Build stage transforms raw source code into a runnable and deployable artifact
- The Test stage acts as a quality gate, identifying bugs before they reach customers
- Deploy stages use patterns like Blue/Green or Canary to minimize production risk
- A professional pipeline combines stages, options, and branch logic into a single file
- Using the 'when' clause ensures that only the 'main' branch reaches production
- Parallel testing within the roadmap significantly improves developer productivity

In the next Module, `Module 5: Declarative Pipelines`, you will dive deeper into the specific directives that make Declarative Pipelines so powerful.
