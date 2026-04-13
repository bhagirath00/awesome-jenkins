# The Agent Directive: Mastering the Workforce

## 1. Introduction: The General and the Specialized Soldiers

Imagine you are a five-star general in a massive, modern army.
You have a mission that requires three very different types of work.
First, you need someone to fly a high-speed jet over enemy territory
to take surveillance photos.
Second, you need a team of engineers to build a bridge over a river.
Third, you need a specialized medical team to set up a field hospital.

If you just shout the order "Go do the work!" without specifying
**who** should do it, you will have a disaster.
The pilot might try to build the bridge, and the medics might try to
fly the jet.
To succeed, you must assign every task to a "Specialized Soldier"
who has the exact skills and tools needed for that task.

In the world of Jenkins and AWS, the **Agent Directive** is how you
assign your tasks to your "Specialized Soldiers."
Your soldiers are the **AWS EC2 instances**, the **Docker containers**,
and the **Kubernetes pods** that make up your infrastructure.
A senior DevOps engineer knows that "one size does not fit all."
You don't run a massive Java compilation on a tiny `t3.micro` instance.
You don't run a simple shell script on a massive, expensive `p3.16xlarge`
GPU instance.
Mastering the agent directive is the key to building a CI/CD system
that is fast, efficient, and cost-effective on the AWS cloud.

In this file, we are going to go into **absolute, extreme technical depth**
on the different ways to define your workforce.
We will look at the global agent, the per-stage agent, and the
modern standard of using Docker containers as your "Disposable Workers."

---

## 2. The Global Agent vs. The Per-Stage Agent

In a Declarative Pipeline, you can define your agent in two places.
Choosing the right place is the first architectural decision you
will make in your Jenkinsfile.

### The Global Agent (The "Default" Worker)
If you define `agent any` at the very top of your pipeline,
Jenkins will find one single machine and use it for the **Entire**
pipeline—from the checkout to the deployment.
- **The Good:** It is simple and easy to understand. It is fast because
  you only have to perform the Git checkout once.
- **The Bad:** It is inflexible. What if your "Build" stage needs
  Java 17 but your "Test" stage needs Java 8? You can't have both
  on the same machine easily.

### The Per-Stage Agent (The "Specialist" Worker)
A senior engineer often sets the top-level agent to `none` and then
defines a specific agent for every single stage.
```groovy
pipeline {
    agent none
    stages {
        stage('Build') {
            agent { label 'high-cpu' }
            steps { sh 'mvn clean install' }
        }
        stage('Test') {
            agent { docker 'python:3.9' }
            steps { sh 'python -m pytest' }
        }
    }
}
```
- **The Good:** You use exactly the right tool for every task.
- **The Bad:** It is slightly slower because Jenkins has to find a
  new machine (or start a new container) for every stage.

---

## 3. Docker Agents: The "Disposable" Workforce

This is the industry standard for modern DevOps on AWS.
Instead of installing 50 different tools (Java, Python, Node, Go)
on your physical EC2 server, you keep the server clean.
When a stage starts, Jenkins launches a **Docker Container**
inside the server.
The work happens inside the container, and when the stage is finished,
the container is destroyed.

### Why Docker Agents are the "Senior" Choice:
1.  **Isolation:**
    If one build needs a specific library that conflicts with another
    build, it doesn't matter. They are in separate containers.
2.  **Immutability:**
    You are 100% certain that the environment is "Perfect" every time.
    There is no "Garbage" left behind by previous builds.
3.  **Portability:**
    If the build works on your laptop in a Docker container, it will
    work on the Jenkins AWS server in the exact same way.

---

## 4. Advanced Agent Options: Mastering the Details

Inside the `agent` block, there are several "Hidden" settings that
separates the juniors from the seniors.

### A. The `label` Strategy
Labels are how you categorize your AWS EC2 instances.
- **`label 'windows'`**: Only run this on a Windows agent.
- **`label 'spot-instance'`**: Run this on a cheap AWS Spot instance
  to save 70% on costs.
- **`label 'ssd'`**: Only run this on a machine with fast NVMe storage.

### B. Custom Workspace
By default, Jenkins creates a workspace folder like `/home/jenkins/workspace/job-name`.
Sometimes, you need a specific path (e.g., if you are building an app
that has hardcoded paths).
```groovy
agent {
    node {
        label 'linux'
        customWorkspace '/opt/my-app-build'
    }
}
```

---

## 5. Summary Table: Choosing Your Worker

| Strategy | When to Use | Senior Rating |
| :--- | :--- | :--- |
| **`agent any`** | Small, simple pipelines. | ⭐️⭐️ |
| **`agent { label '...' }`** | When you need specific hardware (e.g., GPU). | ⭐️⭐️⭐️⭐️ |
| **`agent { docker '...' }`** | **The Professional Standard.** | ⭐️⭐️⭐️⭐️⭐️ |
| **`agent none`** | Complex multi-stage, multi-env pipelines. | ⭐️⭐️⭐️⭐️⭐️ |

---

## Summary

- The agent directive defines which machine or container will execute your pipeline code
- Global agents are simple for small jobs, while per-stage agents allow for specialized task execution
- Using 'agent none' at the top level is a professional strategy for multi-platform pipelines
- Docker agents are the industry standard, providing isolated and immutable build environments
- AWS labels help route jobs to the most cost-effective resources, such as Spot instances
- Custom workspaces can be defined when your build process requires a specific file path
- Balancing build speed against instance cost is a key responsibility for a senior DevOps engineer

In the next file, `03-environment-directive.md`, you will learn how to manage environment variables and safely inject secrets into your pipeline workers.
