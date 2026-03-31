# Jenkins Directory Structure (JENKINS_HOME)

## Introduction

Imagine you are a master architect designed a massive, high-security vault. Inside this vault, you have drawers for blueprints, boxes for secret keys, folders for employee records, and a workbench for active construction. If you don't know where the keys are, or if you accidentally put a firecracker in the records drawer, the entire operation will collapse.

In the world of Jenkins, **JENKINS_HOME** is that vault. 

It is a single directory on your Linux or Windows server where Jenkins stores **everything**. It is the "Brain," the "Memory," and the "Safe" of your entire automation infrastructure. If you lose this directory, your Jenkins server is just a useless, empty program. If you understand this directory, you can fix 90% of all Jenkins crashes, migrate your server to a new AWS region in minutes, and troubleshoot the most complex plugin failures without ever opening the web interface.

As a senior DevOps engineer, you must know this directory structure like the back of your hand. You should be able to navigate it in a terminal while half-asleep. Why? Because when your EBS volume fills up or your EKS Pod crashes, you need to know exactly which files are the "Gold" that must be saved and which ones are the "Trash" that can be deleted to save space.

In this file, we are going to go into **extreme technical depth** on the internal anatomy of Jenkins. We will walk through every major file and folder, explain their role in the Jenkins lifecycle, and talk about the professional strategies for managing this storage on AWS.

---

## 1. Finding the Vault: Where does it live?

Before we look inside, we need to know where the vault is located. This location is defined by the `JENKINS_HOME` environment variable. Depending on your installation method, the default path will vary:

-   **Native Linux (Ubuntu/Debian/RHEL):** `/var/lib/jenkins`
-   **Docker:** `/var/jenkins_home` (inside the container)
-   **Windows:** `C:\ProgramData\Jenkins\.jenkins` or `C:\Jenkins` (depending on your MSI choice)
-   **Kubernetes (EKS):** Usually a Persistent Volume mounted at `/var/jenkins_home`.

**Senior Tip:** In the Jenkins UI, go to **Manage Jenkins** → **System** and look for "Home directory" at the very top. This is the source of truth.

---

## 2. The Master Files: The "Brain" of Jenkins

At the root of the `JENKINS_HOME` directory, you will see several XML files. These are the configuration files that define how Jenkins behaves.

### A. config.xml (The Master Blueprint)
This is the most critical file in the entire system. It is a massive XML file that stores:
-   **Global Settings:** Your Jenkins URL, your admin email, and your system message.
-   **Security Configuration:** Who can log in? Are you using LDAP, GitHub OAuth, or a local database?
-   **Cloud Configuration:** How does Jenkins talk to AWS? (EC2 clouds, EKS clusters).
-   **Global Properties:** Your environment variables (like `AWS_REGION=us-east-1`).
-   **Views:** How your dashboard is organized.

**The Risk:** If you make a single syntax error while editing this file manually, Jenkins will fail to start. Always run `xmlstarlet` or a validator if you script changes to this file.

### B. hudson.model.UpdateCenter.xml
This file tells Jenkins where to look for plugin updates. By default, it points to `https://updates.jenkins.io/update-center.json`. If your server is in a private AWS VPC with no internet access, you will need to point this to a local mirror or an Artifactory instance.

---

## 3. The Core Folders: A Detailed Tour

Let's open the drawers of our vault and see what's inside.

### A. jobs/ (The Heart of the Projects)
This is where your pipelines live. Every job you create in the UI gets its own folder here.
-   **Anatomy of a Job Folder:**
    -   `config.xml`: The specific settings for *that* job (e.g., the Git URL, the build triggers).
    -   `nextBuildNumber`: A tiny text file containing the number of the next run (e.g., `42`).
    -   `builds/`: A folder containing the history of every build. Inside each build folder (like `builds/41/`), you'll find the `log` file (the console output) and the `build.xml` (the result and metadata).

### B. plugins/ (The Superpowers)
This folder contains the `.jpi` or `.hpi` files for every feature you've added.
-   When you install a plugin, Jenkins downloads the `.jpi` file here.
-   On startup, Jenkins "explodes" (unpacks) these files into their own subdirectories.
-   **Senior Secret:** If a plugin update breaks your server, go into this folder, delete the new `.jpi` file, and replace it with the old version. Restart Jenkins, and you've just performed "Emergency Rollback."

### C. secrets/ (The Vault within the Vault)
This is the most sensitive directory in your entire AWS cloud. 
-   **What's inside:** It contains the master encryption keys (`master.key`, `hudson.util.Secret`).
-   **How it works:** Jenkins uses these keys to encrypt your GitHub tokens, AWS Secret Keys, and database passwords.
-   **The Danger:** If you back up your jobs but **NOT** your secrets, you will lose access to all your credentials. When you restore the jobs, Jenkins won't be able to decrypt the passwords, and every pipeline will fail.

### D. users/ (The Identity Drawer)
If you are using the "Jenkins Own User Database," every user gets a folder here with their hashed password and profile settings (like their email and SSH keys).

### E. workspace/ (The Construction Site)
When a build starts, Jenkins needs a place to "Checkout" the code and run the `mvn` or `npm` commands.
-   **The nature:** This folder is **Disposable**. 
-   **The Problem:** This folder is usually the reason your disk fills up. It contains thousands of tiny source files, node_modules, and build artifacts.
-   **Senior Tip:** You can safely delete the entire `workspace/` directory at any time. Jenkins will simply re-clone the code during the next build.

### F. nodes/ (The Muscle)
If you have separate worker machines (Agents) running on EC2, their connection metadata is stored here.

---

## 4. The "Gold" vs. The "Trash": Backup Strategy

As a senior engineer, you must decide what is worth paying for in AWS S3 storage. 

| Item | Category | Backup Priority | Why? |
| :--- | :--- | :--- | :--- |
| `config.xml` | Brain | **Critical** | Defines the entire server state. |
| `secrets/` | Security | **Critical** | Without this, you can't access any credentials. |
| `jobs/*.xml` | Logic | **Critical** | Your pipeline code (unless using Jenkinsfile). |
| `users/` | Accounts | High | Prevents team lockout. |
| `plugins/*.jpi` | Tools | Medium | You can always re-download them, but it's slow. |
| `builds/` | History | Low | Nice to have, but not essential for recovery. |
| `workspace/` | Temp | **None** | Waste of money to backup. Delete it! |

---

## 5. Storage Best Practices on AWS

### A. The EBS "Split" Strategy
Never store your OS and your Jenkins data on the same disk.
1.  **Disk A (Root):** 10-20GB for Ubuntu/Linux.
2.  **Disk B (Data):** 100GB+ EBS volume mounted at `/var/lib/jenkins`.
-   **Benefit:** If the OS gets corrupted, you just detach Disk B and attach it to a brand new server. Your data is safe.

### B. Using Amazon EFS for High Availability
If you are running Jenkins on **Kubernetes (EKS)**, you should use **Amazon EFS** (Elastic File System). 
-   EFS is "Serverless" storage that can be mounted by multiple Kubernetes nodes.
-   If the node running your Jenkins Pod dies, Kubernetes will start a new Pod on a different node. Because of EFS, the new Pod will see the same `JENKINS_HOME` and resume work instantly.

---

## 6. Troubleshooting the "Full Disk" Crisis

Every Jenkins admin eventually gets a Slack message at 3 AM saying: "Jenkins is down! No space left on device."

### Step 1: Find the culprit
```bash
# Find the largest directories in JENKINS_HOME
du -sh /var/lib/jenkins/* | sort -h
```

### Step 2: Clean the Workspaces
```bash
# Delete all workspaces older than 7 days
find /var/lib/jenkins/workspace -type f -atime +7 -delete
```

### Step 3: Check the Logs
Sometimes, a single job goes into an infinite loop and creates a 50GB log file in `logs/`. Delete it and restart the service.

---

## Summary

-   **JENKINS_HOME** is the absolute center of the Jenkins universe.
-   **config.xml** is the master brain; **secrets/** are the master keys; **jobs/** are the work orders.
-   **Persistence** is key: On AWS, always use separate EBS or EFS volumes for your data.
-   **Backups** must focus on the "Gold" (configs and secrets) while ignoring the "Trash" (workspaces).
-   **Maintenance:** Regularly clean your workspaces and build history to keep the "Vault" from overflowing.

In the next chapter, `04-jenkins-configuration.md`, we will take this knowledge of the file system and see how it translates into the "Global System Settings" in the Jenkins web interface.
