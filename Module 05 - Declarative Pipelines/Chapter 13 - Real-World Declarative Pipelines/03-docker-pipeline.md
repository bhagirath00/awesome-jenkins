# The Docker Pipeline: Shipping the Container

## 1. Introduction: The Cargo Ship and the Standardized Box

Imagine you are the manager of a massive, global shipping port.
Before the 1950s, shipping was a nightmare of manual labor and
inefficiency.
If you wanted to ship a piano, you had to hire a specialized team
to wrap it and carry it onto the boat.
If you wanted to ship 1,000 bags of coffee, you had to carry them one
by one.
Every item was a different shape, a different weight, and required
different handling.

Then came the **Shipping Container**.
It was a simple, standardized steel box.
It didn't matter what was inside the box—whether it was a car,
a thousand toys, or a ton of coffee—the cranes, the trucks, and
the ships only had to know how to handle the **Box**.
This "Standardization" revolutionized the global economy, allowing
goods to be moved 100 times faster and 100 times cheaper.

In the world of DevOps, **Docker** is that standardized steel box.
Your application is the "Cargo."
It might be written in Java, Python, or Go. It might need 50 different
libraries to run.
But when you wrap it in a Docker image, your Jenkins server and your
AWS cloud (EKS/ECS) only have to know how to handle the **Container**.
A Docker pipeline is the automated assembly line that builds these
boxes, labels them, and ships them to the digital port (the Registry).

In this file, we are going to go into **absolute, exhaustive technical depth**
on the anatomy of a professional Docker pipeline.
We will look at the lifecycle of a container from the `Dockerfile` to the
**Amazon ECR** registry, and we will learn the senior-level patterns
for tagging and securing your images in a production environment.

---

## 2. The Philosophy of Immutability: The "Frozen" App

A senior DevOps engineer understands that the greatest strength of
Docker is **Immutability**.

### What is Immutability?
It means that once a Docker image is built, it **Never Changes**.
You don't "log into the container" to change a configuration file.
You don't "update the code" inside a running container.
If you want to make a change, you build a **New Image** with a new
version tag and you throw the old one away.

### Why does this matter for CI/CD?
Because the image is frozen, you can be 100% certain that the
exact same code you tested on your laptop is the exact same code
running on the AWS production cluster.
The "Environment" is bundled inside the box.
This eliminates the famous "It works on my machine!" excuse forever.

---

## 3. The Professional Docker Pipeline Blueprint

Here is a full, senior-grade Jenkinsfile for building and shipping a
container to **Amazon Elastic Container Registry (ECR)**.

```groovy
pipeline {
    agent any
    
    environment {
        AWS_REGION = 'us-east-1'
        ECR_REPO = '123456789012.dkr.ecr.us-east-1.amazonaws.com/my-app'
        // Pulling AWS credentials from the vault
        AWS_CREDS = credentials('aws-ecr-login-token')
    }
    
    stages {
        stage('Source: Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Docker: Build Image') {
            steps {
                echo 'Building the Docker image from the Dockerfile...'
                // Using the build number as a temporary tag
                sh "docker build -t ${env.ECR_REPO}:${env.BUILD_NUMBER} ."
            }
        }
        
        stage('Docker: Security Scan') {
            steps {
                echo 'Scanning the image for vulnerabilities...'
                // Using Trivy to check for security bugs
                sh "trivy image ${env.ECR_REPO}:${env.BUILD_NUMBER}"
            }
        }
        
        stage('Docker: Push to AWS ECR') {
            when {
                branch 'main'
            }
            steps {
                echo 'Logging into Amazon ECR...'
                sh "aws ecr get-login-password --region ${env.AWS_REGION} | docker login --username AWS --password-stdin ${env.ECR_REPO}"
                
                echo 'Pushing the "Proven" image to the registry...'
                sh "docker push ${env.ECR_REPO}:${env.BUILD_NUMBER}"
                
                // Also tagging it as "latest" for the deployment system
                sh "docker tag ${env.ECR_REPO}:${env.BUILD_NUMBER} ${env.ECR_REPO}:latest"
                sh "docker push ${env.ECR_REPO}:latest"
            }
        }
    }
    
    post {
        always {
            echo 'Cleaning up local Docker images to save disk space...'
            sh "docker rmi ${env.ECR_REPO}:${env.BUILD_NUMBER}"
            cleanWs()
        }
    }
}
```

---

## 4. Senior Tagging Strategies: More than just "Latest"

A junior engineer uses the `:latest` tag for everything.
A senior engineer knows that `:latest` is a "Moving Target" and is
dangerous for production.

### The Professional Tagging Pattern:
1.  **Build ID Tag (`:42`):** Every single build gets a unique number.
    This provides perfect traceability.
2.  **Commit Hash Tag (`:a1b2c3d`):** This links the image directly
    to the specific line of code in Git.
3.  **Semantic Versioning (`:v1.2.0`):** This is the human-readable
    version used for official releases.

**Rule of Thumb:** Always deploy using a **Specific Version Tag**.
Never use `:latest` in a production AWS EKS deployment, or you will
have no way to perform a clean rollback.

---

## Summary

- Docker provides a "standardized container" for shipping applications across any environment
- The core philosophy of Docker is immutability—built images never change once they are created
- Docker pipelines handle the entire lifecycle: build, security scan, and push to a registry
- Amazon ECR serves as the secure "digital port" for storing and managing your production images
- Senior tagging strategies include using Build IDs and Git hashes for absolute traceability
- Security scanning with tools like Trivy is a mandatory quality gate for containerized apps
- Post-build cleanup of local images is vital for maintaining the health of the Jenkins worker disk

In the next file, `04-deploy-to-kubernetes.md`, you will learn how to deploy these containerized applications to Amazon EKS for massive scale and reliability.
