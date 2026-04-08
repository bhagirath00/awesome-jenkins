# What is a Jenkins Pipeline? The Modern Standard

## 1. Introduction: The "Assembly Line" of Software

Imagine you are standing in the middle of a massive, 21st-century car factory.
In the old days, a single mechanic would stand in one spot and build a whole car.
They would bolt on the wheels, then walk over to get the engine, then install the seats.
This was slow, prone to mistakes, and impossible to scale.
If the mechanic got sick, the whole car stopped being built.

Today, that factory uses an **Assembly Line**.
The car moves through a sequence of "Stages."
At Stage 1, the frame is welded by robots.
At Stage 2, the engine is dropped in with surgical precision.
At Stage 3, the paint is applied in a dust-free chamber.
At Stage 4, the quality inspectors check every single bolt.
The car never stops moving, and every step is perfectly coordinated.

In the world of DevOps, a **Jenkins Pipeline** is that assembly line.
It is a suite of plugins that supports implementing and integrating continuous
delivery pipelines into Jenkins.
A Pipeline provides an extensible set of tools for modeling your entire
software delivery process "as code."
Instead of having a bunch of separate, disconnected jobs, you have one
continuous, logical flow that takes your code from a developer's laptop
all the way to a production AWS environment.

In this file, we are going to go into **unprecedented, senior-level depth** on the
philosophy of Pipelines.
We will look at why the "Pipeline as Code" movement has changed the industry,
how the internal execution engine works on AWS, and why this is the most
important skill you will ever learn as a DevOps engineer.

---

## 2. The Philosophy of "Pipeline as Code"

Before pipelines existed, engineers had to configure their automation by
clicking buttons in a web interface.
This was known as "Click-Ops." While it was easy to start, it was a disaster
for large companies.
If someone accidentally changed a setting, there was no way to know who did it.
If the Jenkins server crashed, you had to manually re-configure hundreds of jobs.

**Pipeline as Code** changed everything.
It means that your entire CI/CD process is written as a text file (the `Jenkinsfile`).
This file lives inside your Git repository, right next to your application code.

### The Benefits for a Senior Engineer:
1.  **Version Control:**
    Every change to your deployment logic is a Git commit.
    You can see exactly what changed, why it changed, and who changed it.
    If a new deployment script breaks the production server, you can "Revert"
    the commit and be back to a working state in seconds.

2.  **Code Review:**
    Before a new deployment step is added, it must go through a Pull Request.
    Other senior engineers can look at the code and say:
    "Wait, this AWS command is insecure, we need to fix it before we merge."

3.  **Auditability:**
    In industries like Finance or Healthcare, you must prove your process.
    Having your entire pipeline in Git provides a perfect audit trail.

4.  **Resiliency:**
    If your Jenkins server is destroyed in an AWS outage, you don't care.
    You just start a new Jenkins server, point it to your Git repo, and
    it automatically recreates all your pipelines from the `Jenkinsfile`.

---

## 3. The Core Concepts: Stages, Steps, and Nodes

A pipeline is built out of three fundamental building blocks.
You must understand these to design a professional automation flow.

### A. The "Node" (The AWS Worker)
A Node is a machine which is part of the Jenkins environment and is
capable of executing a pipeline.
In a professional setup, this is usually an AWS EC2 instance.
The pipeline "Leases" a node to do its work.
- **Senior Tip:** You can have different nodes for different tasks.
- You might have a "Powerful Node" for compiling Java code.
- You might have a "Small Node" for running simple shell scripts.

### B. The "Stage" (The Milestone)
A Stage is a distinct subset of tasks performed through the entire Pipeline.
Stages provide a "Visual Map" of your progress.
Common stages include:
- **Checkout:** Getting the code from Git.
- **Build:** Compiling the application.
- **Test:** Running unit and integration tests.
- **Deploy:** Pushing the code to AWS.

### C. The "Step" (The Individual Task)
A Step is a single task. Fundamentally, a step tells Jenkins **what** to do
at a particular point in time.
Examples of steps:
- `sh 'make'`
- `echo 'Testing finished'`
- `archiveArtifacts 'target/*.jar'`

---

## 4. The "Durability" Factor: Why Pipelines Don't Die

One of the most amazing features of Jenkins Pipelines is **Survivals**.
Imagine you are running a 2-hour-long deployment to a massive AWS cluster.
Suddenly, the power goes out at the data center and your Jenkins server reboots.

In the old "Freestyle" world, that build would be lost forever.
You would have to start from zero and hope it doesn't happen again.

**In the Pipeline world, the build survives.**
Jenkins periodically "Checkpoints" the state of the pipeline to the disk.
When the server comes back online, Jenkins looks at the disk and says:
"Aha! I was at Stage 4 of this build before the crash. I will resume exactly
where I left off."
This "Durability" is why Pipelines are the only choice for mission-critical
enterprise automation.

---

## 5. Summary: Why You Must Master Pipelines

As a senior DevOps engineer, your value is determined by your ability to
automate complex, high-stakes environments.
Freestyle jobs are a great way to learn the basics, but they cannot handle
the scale of a modern tech company.

- **Pipelines** allow you to treat your automation like software.
- **Jenkinsfiles** provide the traceability and security that AWS requires.
- **Stages and Steps** provide a clear, visual way to manage complex workflows.
- **Durability** ensures that your deployments are resilient to failures.

In the next file, `02-pipeline-vs-freestyle.md`, we will do a head-to-head
comparison. We will look at exactly when you should use a pipeline and
why the old way of doing things is slowly being phased out of the
DevOps industry.
