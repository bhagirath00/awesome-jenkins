# First Time Setup Wizard

## Introduction

Imagine you have just built a high-tech skyscraper. You have the foundation, the walls, and the electricity (the installation). But the building is still empty and locked. You need to enter the lobby, use the secret code to open the security doors, and then decide which elevators and lighting systems to turn on. 

In the world of Jenkins, the **First Time Setup Wizard** is that lobby. 

After you have successfully installed Jenkins on your AWS server (as we did in the previous chapter), you can't just start building pipelines immediately. Jenkins starts in a "Locked State" to protect your server from the public internet. You must go through a specific set of steps to "Claim" the server, install the basic tools, and prepare it for its first real DevOps job.

In this file, we are going to walk through the first 10 minutes of your Jenkins server's life. We will go into **extreme depth** on the security mechanics of the "Unlock" screen, the philosophy behind the "Suggested Plugins," and the common traps that junior engineers fall into when they are in a hurry to get past these screens.

---

## 1. Unlocking Jenkins (The Security Gate)

The very first time you visit `http://your-aws-ip:8080`, you will see a screen titled **"Unlock Jenkins."** It will ask for an "Administrator password."

### Why is this necessary?
Jenkins is an incredibly powerful tool. By default, it has the ability to run shell scripts and talk to your AWS infrastructure. If Jenkins started up completely open to the internet, a hacker could find your server in minutes and use it to mine Bitcoin or steal your data. 

To prevent this, Jenkins creates a **Secret Key** the very first time it starts. This key is hidden deep inside the server's filesystem. Only someone who has direct SSH access to the server (you) can read this key.

### How to find the Secret Key:
You must log into your Linux server via SSH and run this command:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

**Senior Tip:** If you are using **Docker**, the command is slightly different. You must "exec" into the container to see the file:
```bash
docker exec <container_id> cat /var/jenkins_home/secrets/initialAdminPassword
```

Copy that long, random string (e.g., `4a9e6b8c2d1f...`) and paste it into the box. Once you click "Continue," the initial password file is automatically deleted for security. You have now proven that you are the owner of the server.

---

## 2. The Plugin Decision: "Suggested" vs. "Manual"

The next screen presents you with two choices:
1.  **Install suggested plugins**
2.  **Select plugins to install**

### The Senior Engineer's Philosophy
99% of the time, you should click **"Install suggested plugins."** 

**Why?** Because the Jenkins community has been refining this list for over a decade. These are the "Essential Organs" of Jenkins. If you skip this, your Jenkins server will be "Brain Dead." You won't be able to talk to Git, you won't be able to write Pipelines, and you won't even be able to see beautiful colors in your logs.

### What is actually being installed? (A Deep Look)
While the progress bar is moving, Jenkins is downloading and installing the following tools:

| Plugin Category | Essential Plugins | Why you need them |
| :--- | :--- | :--- |
| **Source Control** | Git, GitHub, Subversion | You cannot do DevOps without code. These allow Jenkins to "Pull" your code. |
| **Pipeline** | Pipeline, Workflow-Aggregator | This is the core engine of modern "Jenkins-as-Code." |
| **Security** | Matrix Authorization, LDAP | These allow you to control who can see and run your builds. |
| **Notifications** | Email Extension, Mailer | Essential for "Fast Feedback." Tells the team when a build fails. |
| **User Interface** | Workspace Cleanup, Timestamper | Makes the UI cleaner and the logs easier to read. |

**Troubleshooting:** If a plugin fails to install (usually because of a temporary network glitch on AWS), don't panic. You can simply click "Retry" or click "Continue" and install it later from the Plugin Manager.

---

## 3. Instance Configuration: The Identity of Jenkins

Once the plugins are done, Jenkins will ask you for the **Jenkins URL**.

### The Junior Mistake
Many people just leave this as `http://localhost:8080/` or `http://10.0.0.45:8080/` (the private AWS IP). 

**Why this is a disaster:** 
Jenkins uses this URL to create links. If Jenkins sends an email to a developer saying "Build Failed," the developer clicks the link. If the link points to `localhost`, it won't work on their computer! Even worse, if you set up **GitHub Webhooks**, GitHub will try to send notifications to that URL. If the URL is private or wrong, the automation will never trigger.

### The Professional Way
- Use the **Public IP** of your EC2 instance (e.g., `http://54.123.45.67:8080/`).
- Even better, use a **DNS Name** if you have one (e.g., `https://jenkins.mycompany.com`).

---

## 4. The "Success" Screen: Jenkins is Ready!

After you click "Save and Finish," you are taken to the main Jenkins Dashboard. It will be empty, saying "Welcome to Jenkins!"

This is the moment where the "Installation" phase ends and the "Operations" phase begins. You now have a working, secured engine that is ready to be configured for your specific AWS projects.

---

## 5. Common Setup Wizard Traps

### A. The "Offline" Error
Sometimes, Jenkins says "This instance appears to be offline" during the plugin setup.
- **The Senior Analysis:** This usually means your AWS Security Group doesn't allow "Outbound" traffic on port 443 (HTTPS), or your server is behind a corporate proxy.
- **The Fix:** Ensure your server can talk to `https://updates.jenkins.io`.

### B. The "Incompatible Plugin" Warning
Occasionally, a suggested plugin might be too new for the version of Jenkins you installed.
- **The Senior Analysis:** This usually happens if you used an old Linux package repository.
- **The Fix:** Always ensure you follow the repository steps in Chapter 3.1 to get the **LTS (Long Term Support)** version.

---

## Summary

-   **The Unlock Screen** is your first security check. Only those with server-level access can pass.
-   **Suggested Plugins** provide the 20+ core features needed for any modern DevOps pipeline.
-   **The Jenkins URL** is the "Public Address" of your server. If it's wrong, your external integrations (like GitHub and Email) will break.
-   **The Setup Wizard** is a one-time process. Once finished, the `initialAdminPassword` file is deleted forever.

In the next file, `02-admin-user-setup.md`, we will learn how to create your permanent administrator account and how to start managing your team's access to the server.
