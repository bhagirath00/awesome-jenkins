# Installing Jenkins on Linux (Production-Grade Guide)

## Introduction

Imagine you have just been appointed as the Lead DevOps Engineer for a global financial institution. Your first mission is to build the central nervous system of the company's software delivery: the Jenkins server. This isn't just a "box in the corner" running a few scripts. This is a mission-critical piece of infrastructure that will manage the code for thousands of banking applications. 

If this server is slow, the entire company's release cycle grinds to a halt. If it's insecure, hackers could gain access to the production environment and manipulate financial data. If it crashes, 2,000 developers are suddenly blocked from doing their jobs.

In this file, we are going to go into **absolute, extreme depth** on how to install Jenkins on Linux. We will focus on the two most common distributions used in the enterprise world: **Ubuntu 22.04 LTS** and **Amazon Linux 2/2023**. We won't just give you a "checklist" of commands. We will explore the architectural "Why" behind every decision—from choosing the right AWS instance type to configuring the underlying Java Virtual Machine (JVM) for maximum performance.

---

## 1. Preparing the AWS Infrastructure (The Foundation)

Before you ever run a `sudo apt install` command, you must prepare the "Terrain" where your server will sit. In a professional AWS environment, this is a multi-step process that involves compute, storage, and networking.

### A. Choosing the Right Instance Type (The "Hardware" Tax)
Jenkins is a Java application. Java applications are notorious for being "RAM-Hungry." Furthermore, Jenkins doesn't just "sit there"; it performs heavy tasks like compiling Java code, zipping files, and running security scans.

| Instance Class | Specs | Use Case | Result |
| :--- | :--- | :--- | :--- |
| **t2.micro** | 1 vCPU, 1GB RAM | Learning only. | **CRASHES** in production. |
| **t3.medium** | 2 vCPU, 4GB RAM | Small teams (5-10 people). | Stable for simple jobs. |
| **t3.large** | 2 vCPU, 8GB RAM | **Standard Team Choice.** | Smooth performance for Docker builds. |
| **m5.xlarge** | 4 vCPU, 16GB RAM | Large Enterprise. | Can handle 100+ concurrent users. |

**Senior Rule:** Never launch a production Jenkins server on anything less than a `t3.medium`. If you do, the Linux kernel will eventually "kill" the Jenkins process to save the OS from crashing (this is called an OOM - Out of Memory event).

### B. Configuring the AWS Security Group
Your Jenkins server is a "High Value Target" for hackers. You must protect it with a layered defense.
1.  **Port 22 (SSH):** Allow ONLY from your specific IP or your company's VPN CIDR.
2.  **Port 8080 (Web UI):** This is the default Jenkins port. Allow from your office or VPN.
3.  **Port 443 (HTTPS):** In production, you will use an SSL certificate. This is the standard entrance.
4.  **Port 50000 (JNLP):** Used for Agents to talk to the Controller. This should **NEVER** be open to the internet. Open it only to the CIDR of your VPC.

### C. EBS Storage (gp3)
Jenkins stores every build log, every artifact, and every plugin on disk. 
- Use **Amazon EBS gp3** volumes. They offer a baseline of 3,000 IOPS for free, which is essential for Jenkins because it does a lot of small file reads/writes (the "I/O" bottleneck).

---

## 2. The Foundation: Installing the Java Runtime (JDK)

Jenkins is a Java application. It requires a **Java Development Kit (JDK)** to run. 

### Why the version matters
As of early 2024, Jenkins officially supports **Java 11 and Java 17**. 
- **Java 8:** Deprecated. Do not use. 
- **Java 21:** Supported in newer versions, but many old plugins might break.
- **The Senior Choice:** **Java 17 (LTS)**. It is the perfect balance of modern features and rock-solid stability.

### Step-by-Step Installation on Ubuntu:
```bash
# Update the local package index to see the latest versions
sudo apt update -y

# Install the OpenJDK 17 package. 
# We use the -y flag to automatically say 'Yes' to all prompts
sudo apt install openjdk-17-jdk -y

# Verify the installation. 
# Look for 'openjdk version "17.0.x"' in the output.
java -version
```

### Step-by-Step Installation on Amazon Linux 2 (AL2):
```bash
# Amazon Linux uses the extras repository for modern packages
sudo amazon-linux-extras install java-openjdk11 -y

# Alternatively, for Java 17 on newer AL2023:
sudo yum install java-17-amazon-corretto -y
```

---

## 3. The Installation: Adding the Jenkins Repository

We never install Jenkins by downloading a random `.deb` or `.rpm` file. Why? Because you won't get automatic security updates. Instead, we add the **Official Jenkins Repository** to our system.

### The Ubuntu/Debian Method:
```bash
# 1. Download the GPG Key. 
# This key is a digital signature that verifies the software is authentic.
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

# 2. Add the repository to the sources list.
# We use the 'signed-by' flag to link the repo to the key we just downloaded.
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/" | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

# 3. Update the package lists again
sudo apt update

# 4. The Final Install command
sudo apt install jenkins -y
```

### The Amazon Linux / RHEL Method:
```bash
# 1. Download the repo file
sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo

# 2. Import the public key
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

# 3. Upgrade your packages and install
sudo yum upgrade
sudo yum install jenkins -y
```

---

## 4. Starting and Enabling the Jenkins Service

In modern Linux, services are managed by **systemd**. You must tell the OS that Jenkins is a "Service" that should be kept running forever.

### Commands for the Terminal:
```bash
# Start the Jenkins process
sudo systemctl start jenkins

# Tell Linux to start Jenkins every time the server boots up.
# If you don't do this, and your AWS instance reboots, Jenkins will stay dead.
sudo systemctl enable jenkins

# Check the heartbeat of the service
sudo systemctl status jenkins
```

### Troubleshooting a "Failed" Status:
If you see `Active: failed`, run this command to see the hidden error logs:
```bash
journalctl -u jenkins --no-pager | tail -n 50
```
**Common Errors:**
- `Address already in use`: Another program is using port 8080.
- `Invalid Java version`: You have Java 8 installed instead of 17.
- `Permission denied`: You messed up the permissions on the `/var/lib/jenkins` folder.

---

## 5. Post-Installation: Unlocking the Gate

Jenkins starts in a "Locked" mode. It creates a temporary password that only the server administrator (you) can see. This prevents "Drive-by Hackers" from taking over your new server.

### How to find the Secret Code:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
The output will be a 32-character string like `a1b2c3d4e5f6g7h8i9j0...`. 
- **Action:** Copy this string. You will need it in the next chapter when we log into the web browser.

---

## 6. Senior Troubleshooting: The "Pro" Checklist

After installing Jenkins hundreds of times, these are the "Gotchas" that save me hours of debugging.

### A. The "Java 8" Ghost
If your server already had Java 8, and you installed Java 17, Jenkins might still try to use the old version.
- **Fix:** Use `sudo update-alternatives --config java` and choose the version pointing to Java 17.

### B. Changing the Default Port
What if you want to run Jenkins on port 9090 instead of 8080?
- **Ubuntu:** Edit `/etc/default/jenkins` and change `HTTP_PORT=8080`.
- **AL2/RHEL:** Edit `/etc/sysconfig/jenkins` or use `systemctl edit jenkins`.
- **Restart:** You must run `sudo systemctl restart jenkins` for the change to take effect.

### C. The "Out of Memory" (OOM) Killer
If Jenkins randomly stops, check the system logs:
```bash
grep -i "killed process" /var/log/syslog
```
If you see "Out of memory," it means your EC2 instance is too small. **Immediately upgrade to a t3.large.**

---

## Summary

-   **Linux is the King of CI/CD:** 95% of production Jenkins instances run on Linux.
-   **Java 17 (LTS)** is the engine that powers modern Jenkins.
-   **Security Groups** and **Firewalls** are your first line of defense in AWS.
-   **Repositories** ensure you get security patches automatically via `apt upgrade`.
-   **Systemd** keeps your automation alive 24/7.
-   **Secrets:** The `initialAdminPassword` is your key to the kingdom—keep it safe.

In the next file, `02-install-docker.md`, we will look at how to run Jenkins as a containerized service, which is the preferred method for teams that want absolute isolation and "Portable" automation.
