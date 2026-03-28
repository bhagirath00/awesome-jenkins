# Jenkins Use Cases

## Introduction

Imagine you are working at a fast-growing startup that builds a food delivery app. Your team is pushing code every hour. Without any automation, your day would look like a nightmare. You would have to manually log into servers, pull code, run tests, build Docker images, and push them to AWS. If you make one tiny mistake at 4 PM on a Friday, the whole app goes down, and you spend your weekend fixing it.

This is where Jenkins comes in. We don't just use Jenkins because it's a "popular tool." We use it because it handles all the boring, repetitive, and dangerous tasks for us. It acts like a robotic assistant that never sleeps and never makes a mistake.

In this file, we are going to look at the real-world situations where Jenkins saves the day. We will go deep into how it handles building code, deploying to AWS, checking for security holes, and even managing your cloud servers.

---

## 1. The Classic Build and Test (CI)

This is the most basic, yet most important job for Jenkins. Every time a developer says, "I'm done with this feature," and pushes their code to GitHub, Jenkins jumps into action.

### The Story
Think of Jenkins like a high-end car factory. Before a car leaves the factory, it goes through a "test track." If the brakes don't work or the engine makes a weird noise, the car isn't allowed to leave. Jenkins is that test track for your code.

It pulls the code, compiles it, and runs hundreds of "Unit Tests." If a developer accidentally broke the login page, Jenkins will find it in minutes. It stops the broken code from moving forward and sends a message saying, "Hey, you broke the login logic! Fix it before we try to deploy."

### Practical Code Example
Here is what a simple "Build and Test" stage looks like in a Jenkinsfile for a Java app:

```groovy
stage('Build and Test') {
    steps {
        echo 'Step 1: Compiling the code...'
        sh 'mvn clean compile'
        
        echo 'Step 2: Running Unit Tests...'
        sh 'mvn test'
    }
    post {
        always {
            // This shows the test results in a nice graph in Jenkins
            junit '**/target/surefire-reports/*.xml'
        }
    }
}
```

---

## 2. Deploying to AWS (Continuous Delivery)

Once the tests pass, the code needs to go somewhere. In most modern companies, that "somewhere" is AWS. Jenkins is excellent at talking to AWS services like **Amazon ECR** (for storing images) and **Amazon ECS** (for running containers).

### The Story
Imagine you have to deliver 100 pizzas. Instead of driving back and forth 100 times, you use a massive delivery truck. In the software world, that "truck" is a **Docker Container**.

Jenkins builds the container, puts your fresh code inside it, and "drives" it to the **Amazon Elastic Container Registry (ECR)**. Once it's there, Jenkins tells **Amazon ECS** to stop the old version of the app and start the new one. This is called a "Rolling Update." Your users never even notice the switch; the app just stays online and gets better.

### Practical AWS Example
This is how Jenkins pushes a Docker image to AWS:

```groovy
stage('Push to AWS ECR') {
    steps {
        script {
            // Login to AWS ECR using the AWS CLI
            sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789.dkr.ecr.us-east-1.amazonaws.com"
            
            // Build the image with a unique tag
            sh "docker build -t my-app:${env.BUILD_NUMBER} ."
            
            // Push it to the registry
            sh "docker push 123456789.dkr.ecr.us-east-1.amazonaws.com/my-app:${env.BUILD_NUMBER}"
        }
    }
}
```

---

## 3. Infrastructure as Code (IaC)

Jenkins doesn't just manage your code; it can manage your "hardware" too. In the cloud, we don't buy physical servers. We write code (using tools like **Terraform**) that tells AWS to create servers for us. 

### The Story
Think of this like a "Magic Blueprint." You show the blueprint to Jenkins, and Jenkins builds the entire building (the servers, the database, the network) on AWS exactly like the drawing says. If you need a second building tomorrow, you just give Jenkins the same blueprint.

DevOps engineers use Jenkins to run Terraform. This ensures that the environment is built exactly the same way every time. No more "I forgot to click the 'Enable Public IP' button in the AWS console."

### Practical IaC Example
```groovy
stage('Provision Infrastructure') {
    steps {
        // Initialize Terraform
        sh 'terraform init'
        
        // Show us what will change before we do it
        sh 'terraform plan -out=tfplan'
        
        // Wait for a human to look at the plan and say "Yes"
        input message: 'Does the AWS plan look correct?'
        
        // Build the servers on AWS
        sh 'terraform apply tfplan'
    }
}
```

---

## 4. Security Scanning (DevSecOps)

Security is often the "forgotten child" in software development. People usually check for security holes right before the big release. By then, it's too late and too hard to fix. 

Jenkins allows you to do **"DevSecOps,"** which means checking for security issues *every single time* you build the code.

### The Story
Imagine you are building a bank vault. You wouldn't wait until the vault is finished to check if the lock works. You check the steel while you're pouring the concrete. 

Jenkins runs tools like **SonarQube** (to find bugs in the code) and **Trivy** (to find vulnerabilities in your Docker images). If Jenkins finds a "Critical" security hole, it fails the build immediately. It says, "Stop! This code has a security flaw. Fix it before we let this anywhere near our customers."

### Security Tools Comparison

| Tool | What it Scans | Why we use it in Jenkins |
| :--- | :--- | :--- |
| **SonarQube** | The Source Code | Finds "bugs" and "code smells" that could cause crashes. |
| **Trivy** | Docker Images | Checks if the base OS (like Alpine or Ubuntu) has known security holes. |
| **Snyk** | Dependencies | Checks if the libraries you are using (like Log4j) are safe. |
| **Checkov** | Terraform Code | Makes sure you didn't accidentally leave your AWS S3 bucket open to the public. |

---

## 5. Scheduled Maintenance and Backups

Jenkins isn't always reacting to a developer's push. Sometimes, it just does "housekeeping." You can tell Jenkins to run a job every night at 2 AM or every Sunday morning.

### The Story
Think of this like a "Night Shift Janitor." While everyone is sleeping, Jenkins is busy cleaning up old logs, backing up the database to **Amazon S3**, and making sure the servers are healthy. 

If a database backup fails at 3 AM, Jenkins will have an alert waiting for you in Slack when you wake up. You don't have to remember to do it; Jenkins handles the routine.

---

## 6. Multi-Cloud Management

While we love AWS, sometimes a big company might have some servers in a different cloud or even in their own office. Jenkins is the "Bridge" that connects them all. 

Because Jenkins is so flexible, it can talk to AWS, Google Cloud, and your local servers at the same time. You can have a pipeline that builds code on a local machine and then deploys it to **Amazon EKS** (Kubernetes) in the cloud.

---

## Summary

Jenkins is the multi-tool of the DevOps world. We use it for:
- **CI (Build & Test):** Automatically checking if the code works.
- **CD (Deployment):** Moving code to AWS ECR and ECS without manual effort.
- **IaC:** Using Terraform to build AWS infrastructure from a "blueprint."
- **Security:** Checking for vulnerabilities early (Shift Left).
- **Maintenance:** Running nightly backups and cleanups.

By using Jenkins for these tasks, you free yourself from boring manual work. You stop being a "Firefighter" who is always fixing broken deployments and start being an "Architect" who builds reliable systems.

In the next file, `04-jenkins-in-devops.md`, we will look at how Jenkins fits into the broader "DevOps Culture." We will see how it helps different teams (like Developers and Operations) finally stop fighting and start working as one team.
