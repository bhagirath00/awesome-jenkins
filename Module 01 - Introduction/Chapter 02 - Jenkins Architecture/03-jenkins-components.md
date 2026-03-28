# Jenkins Components

## Introduction

If you open the hood of a high-performance car, you don't just see "an engine." You see a fuel pump, a turbocharger, an alternator, and a radiator. To be a master mechanic, you need to know what each part does and how they talk to each other. 

Jenkins is exactly the same. It isn't just one big blob of code; it is a collection of internal "organs" that each have a specific job. If one organ fails, the whole system can crash.

In this file, we are going to look at the internal components of Jenkins. We will see where your code lives while it's being built, how Jenkins remembers your history, and why the **JENKINS_HOME** folder is the most valuable piece of data in your entire AWS cloud.

---

## 1. JENKINS_HOME (The Memory)

Every Jenkins server has a home. This is a folder on the hard drive called **JENKINS_HOME**. 

### The Story
Imagine you have a diary where you write down every single thing you did today, every password you have, and every plan you have for tomorrow. If you lose that diary, you are in big trouble. **JENKINS_HOME is that diary.**

In a professional AWS setup, we don't just leave this folder on a random server. We store it on an **Amazon EBS Volume** (Elastic Block Store) or **Amazon EFS** (Elastic File System). This way, even if the EC2 instance (the "body" of the server) dies, we can simply plug the EBS volume into a new instance, and Jenkins will "wake up" with all its memories intact.

### What's inside the diary?
-   **config.xml:** The "Brain" file. It stores the main settings for the whole server.
-   **jobs/:** A folder containing every job you have ever created. Each job has its own `config.xml` and its entire build history.
-   **plugins/:** A folder containing all the extra tools you have added (like the AWS or Git plugins).
-   **secrets/:** The most sensitive part. This is where Jenkins hides the keys to encrypt your passwords.

---

## 2. The Build Queue and Executors

When you click "Build Now," the job doesn't always start instantly. Sometimes it has to wait in line.

### The Build Queue (The Waiting Room)
Think of this like a waiting room at a doctor's office. You check in at the front desk (you click build), but you have to sit in a chair until a room is ready for you. 

Jobs stay in the queue if:
-   All the "Cooks" (Agents) are currently busy.
-   The specific machine you asked for (e.g., a "Windows Machine") is offline.

### The Executor (The Doctor's Room)
An Executor is the actual "slot" where the build happens. 
-   If an Agent has **2 Executors**, it can handle 2 jobs at the same time. 
-   If you have 100 jobs to run but only 5 executors, Jenkins will run 5 at a time and keep 95 in the queue.

---

## 3. The Workspace (The Construction Site)

The **Workspace** is a folder on the **Agent** machine where the actual building happens. 

### The Story
Imagine you are building a wooden table. You need a workbench. You put your wood, your saw, and your glue on the workbench. Once the table is finished, you clean up the workbench so you can build the next thing.

**The Workspace is that workbench.**
1.  Jenkins creates a folder for your project.
2.  It pulls your code from GitHub into that folder.
3.  It runs the tests and builds the Docker image.
4.  Once finished, the workspace is often cleared out to save space.

**Senior Rule:** Never store important files in the workspace permanently. If you need to keep a file (like a JAR or a Report), you must "Archive" it (move it to the permanent storage).

---

## 4. Artifacts (The Finished Product)

An **Artifact** is a file produced by a build that we want to keep forever. 

### The Story
In our table-building analogy, the "Artifact" is the finished table. You wouldn't throw the table away when you clean the workbench! You move the table to the showroom.

In Jenkins, when a build finishes, it can copy important files (like a `deployment.zip` or a `report.pdf`) from the temporary **Workspace** on the Agent back to the permanent **JENKINS_HOME** on the Controller. This way, you can go back to "Build #54" from a month ago and download the exact file that was built that day.

---

## 5. The Update Center

Jenkins is famous for its plugins. But where do they come from? The **Update Center** is the internal part of Jenkins that talks to the internet (specifically `updates.jenkins.io`).

It checks for new versions of the Docker plugin, the AWS plugin, and the Git plugin. When you click "Install," the Update Center downloads the file and puts it into your `plugins/` folder.

---

## Jenkins Internal Components Summary Table

| Component | What it does | Where it lives |
| :--- | :--- | :--- |
| **JENKINS_HOME** | Stores all permanent settings and history. | Controller (EBS Volume) |
| **Build Queue** | Manages the list of jobs waiting to run. | Controller (Memory) |
| **Executor** | The slot that runs a build. | Agent (CPU/Thread) |
| **Workspace** | The temporary folder for building code. | Agent (Hard Drive) |
| **Artifact Store** | Stores the finished results of a build. | Controller (Hard Drive) |
| **Update Center** | Manages downloading and updating plugins. | Controller (Memory) |

---

## Summary

Jenkins is a symphony of moving parts.
-   **JENKINS_HOME** is the most important folder; it holds the entire state of your automation.
-   **Executors** are the workers that define how much work you can do at once.
-   **The Workspace** is a temporary area where the work happens, while **Artifacts** are the permanent results we save.
-   **The Update Center** keeps your tools fresh by connecting to the community plugin repository.

Understanding these components makes you a better DevOps engineer because you know exactly where to look when something breaks. If the server is slow, check the **Executors**. If the history is gone, check **JENKINS_HOME**. 

You have now completed **Module 1: Introduction**. You have mastered the theory, the architecture, and the internal components of Jenkins.

In the next Module, **Module 2: Installation**, we are going to get our hands dirty. We will launch an EC2 instance in AWS and install Jenkins from scratch so you can start building your first pipelines!
