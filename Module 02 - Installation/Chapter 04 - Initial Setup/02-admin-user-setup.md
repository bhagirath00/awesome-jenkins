# Admin User Setup

## Introduction

Imagine you have just built the most advanced automated factory in the world. You have the machines, the conveyor belts, and the robots. Now, you need to decide who holds the "Master Key." If you give the master key to everyone, a new employee might accidentally press the "Emergency Stop" or, worse, the "Delete All Data" button. If you lose the master key, the entire factory is locked forever.

In Jenkins, the **Admin User** is the holder of that master key. 

This account has total control over the server. It can see your **AWS Access Keys**, it can change the code of your production pipelines, and it can delete the entire history of your company's builds. In a production environment, managing this account is not just about "picking a password"; it's about setting up a secure foundation for your entire engineering team.

In this file, we are going to go into **extreme depth** on how to create your first admin user, how to manage permissions for a growing team, and how to "break back into" your server if you ever get locked out of your own admin account.

---

## 1. Creating the First Admin Account

As soon as the plugins are finished installing (from the previous chapter), Jenkins will ask you to create your first user. 

### The Junior Habit vs. The Senior Practice
- **The Junior Habit:** Many beginners just click "Continue as Admin" and keep using the random password from the `initialAdminPassword` file. 
- **The Problem:** That initial password is hard to remember and is meant to be temporary. If you lose it, you are locked out.
- **The Senior Practice:** Always create a **Dedicated Admin User** immediately. 

### The Setup Checklist:
1.  **Username:** Do not use `admin`. This is the first name every hacker tries. Use something specific to your role, like `devops-lead` or `techcorp-admin`.
2.  **Password:** Use a high-entropy password (at least 16 characters). This account is the "Keys to the Kingdom." If this account is compromised, your AWS cloud is at risk.
3.  **Full Name:** Use your real name or the team name (e.g., "Build Master"). This helps in the audit logs so you know who changed a job at 3 AM.
4.  **E-mail Address:** Use a valid work email. Jenkins uses this to send security alerts if someone tries to hack the server.

---

## 2. The "Principle of Least Privilege" (PoLP)

Once your admin account is ready, you will eventually want to invite your team. This is where most DevOps security fails.

### What is PoLP?
The principle of Least Privilege means giving a user **the absolute minimum access they need to do their job, and nothing more.**

- **The Developer:** Needs to see build results and click "Build Now." They do **NOT** need to install plugins or see the Global System Config.
- **The QA Engineer:** Needs to see test reports and artifacts. They do **NOT** need to see AWS credentials.
- **The Junior DevOps:** Might need to manage agents but shouldn't be able to delete the production deployment pipeline.

### Managing Users in Jenkins:
Go to **Manage Jenkins** → **Users**. This is where you create accounts. However, Jenkins' basic security is "All or Nothing." To do real permission management, you will eventually need the **"Role-based Strategy Plugin"** (which we will discuss in later modules).

---

## 3. Account Hardening: Protecting the Master Key

Setting a password is only the first step. To truly secure your Jenkins admin account on AWS, you must follow these hardening steps:

### A. Disable Public Sign-ups
By default, some versions of Jenkins allow anyone with the URL to "Sign Up" for an account. 
1. Go to **Manage Jenkins** → **Security**.
2. Find the "Security Realm" section.
3. Ensure **"Allow users to sign up"** is **UNCHECKED.** 
4. If you leave this checked, a hacker can simply create their own account and start searching for holes in your server.

### B. Use a Strong Security Realm
While Jenkins can store its own users, in a professional company, you should connect Jenkins to your **Active Directory** or **Okta** (using LDAP or SAML). This allows you to manage users in one central place. When someone leaves the company, their Jenkins access is automatically revoked.

---

## 4. Disaster Recovery: What if you are Locked Out?

It happens to everyone. You change the password and forget it, or your LDAP server goes down, and suddenly you can't log into your own Jenkins server.

### The "Senior Backdoor" Method:
Since you have SSH access to the AWS server, you can "Turn Off" security manually to regain access.

1.  SSH into your Linux server.
2.  Navigate to the home directory: `cd /var/lib/jenkins`.
3.  Open the main config file: `sudo nano config.xml`.
4.  Find the line: `<useSecurity>true</useSecurity>`.
5.  Change it to: `<useSecurity>false</useSecurity>`.
6.  Restart Jenkins: `sudo systemctl restart jenkins`.
7.  Now, when you visit the UI, Jenkins will let you in without a password. **Fix your user settings immediately**, then turn security back on and restart again.

---

## 5. Audit Logging: Who did what?

A senior engineer doesn't just manage users; they watch them. If a production build fails because someone changed a setting, you need to know who it was.
- Install the **Audit Log Plugin**.
- This plugin records every time an admin logs in, every time a job is created, and every time a global setting is changed. This is a requirement for SOC2 and ISO security compliance.

---

## Summary

-   **The Admin Account** is the most powerful entity in your Jenkins server. Protect it at all costs.
-   **Security by Obscurity:** Avoid the username `admin` to prevent automated brute-force attacks.
-   **Least Privilege:** Never give a developer more access than they need to run their builds.
-   **Disable Sign-ups:** Ensure that your Jenkins server is a "Closed System" from day one.
-   **The Backdoor:** You can always reset security by editing `config.xml` if you have SSH access to the server.

In the next file, `03-jenkins-directory-structure.md`, we will move away from the web interface and look at how these user accounts and settings are actually stored on the Linux hard drive.
