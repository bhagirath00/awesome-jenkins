# Creating Your First Freestyle Job: A Step-by-Step Masterclass

## Introduction: The "Hello World" of Automation

Imagine you have just been handed the keys to a high-tech automated manufacturing plant. You have thousands of robots, hundreds of conveyor belts, and a massive control room. But right now, the plant is silent. Your first task isn't to build a complicated car or a jet engine. Your first task is to make one single robot pick up a block and put it down. This is the "Hello World" of automation. 

In Jenkins, the **Freestyle Job** is that first step. 

While the industry is moving towards "Pipelines as Code," every senior DevOps engineer must first master the Freestyle job. Why? Because it teaches you the fundamental mechanics of how Jenkins interacts with the operating system, how it handles files, and how it reports errors. If you can't build a simple Freestyle job, you will never be able to debug a complex multi-stage pipeline.

In this file, we are going to go into **extreme, exhaustive depth** on the process of creating your very first job. We will walk through the "New Item" screen, explore the configuration UI in painstaking detail, and build a "System Status Monitor" that will run real-world Linux commands on your AWS server. We will learn how to read the console logs like a detective and how to troubleshoot the most common errors that plague junior engineers.

---

## 1. The "New Item" Screen: Choosing Your Mission

To start your journey, you must find the **New Item** link on the top-left of your Jenkins dashboard. This is the "Birthplace" of every automated task on your server.

### A. Naming Your Job (The Senior Rulebook)
A junior engineer walks into the room and names their job `test`, `build1`, or `asdf`. 
**DO NOT DO THIS.**
In a professional AWS environment, you might eventually have 5,000 jobs. If they are named poorly, you will never find anything. 

**The Senior Naming Strategy:**
- **No Spaces:** Spaces in job names create "Hell" in Linux terminal commands. Use hyphens (`-`) or underscores (`_`).
- **Use Categorization:** Start with the project name or the environment.
- **Example:** `PROD-WEBSITE-HEALTH-CHECK`

### B. Selecting the "Freestyle Project"
You will see a list of different icons. For this exercise, select **Freestyle project**.
- **What is it?** It is a "Blank Canvas." It allows you to build anything by combining SCM, Triggers, and Build Steps in a visual way.
- **Why now?** It is the easiest way to get immediate feedback and learn the "Core Six" sections of a Jenkins job.

Click the **OK** button at the bottom. You have now officially created the "Shell" of your job.

---

## 2. Configuration: The "General" Tab Deep Dive

You are now looking at a very long page. This is the "Instruction Manual" for your job. Let's look at the "General" section in extreme detail.

### A. The Description (The "Documentation" Phase)
Most engineers skip this. **A senior engineer NEVER skips this.**
In the description box, you should use **HTML** or **Markdown** to describe:
1.  **Purpose:** What does this job do? (e.g., "This job monitors the disk space and RAM of the main EC2 instance every hour.")
2.  **Owner:** Who should be contacted if this job fails? (e.g., "@devops-on-call on Slack").
3.  **Links:** Provide a link to the relevant AWS CloudWatch dashboard or the GitHub repo.

### B. The "Discard Old Builds" Strategy
Every time a job runs, it creates a folder on your AWS EBS volume. If your job creates a 100MB artifact and runs every hour, you will fill up 2.4GB of storage **every single day.** 
- **Check this box.** 
- **Strategy:** "Log Rotation."
- **Days to keep builds:** Set to `7`. (Most developers don't care about a build result from 3 weeks ago).
- **Max # of builds to keep:** Set to `10`. (This keeps your history clean and your disk happy).

---

## 3. The "Build Steps" Section: Giving the Robot Its Commands

Scroll down past the "SCM" and "Triggers" (we will cover those in later chapters) until you find **Build Steps**. This is the "Heart" of the job. This is where the work happens.

1.  Click the **Add build step** dropdown.
2.  Select **Execute shell** (if you are on a Linux server) or **Execute Windows batch command** (if you are on a Windows server).
3.  A black text box appears. This is your terminal.

### Our "System Monitor" Script:
We want to write a script that gives us a professional report of our server's health. Copy and paste this into the box:

```bash
#!/bin/bash
# The 'set -e' tells the script to STOP immediately if any command fails.
set -e

echo "--- STARTING SYSTEM HEALTH REPORT ---"
echo "Timestamp: $(date)"
echo "------------------------------------"

echo "STEP 1: Checking System Uptime"
uptime
echo "------------------------------------"

echo "STEP 2: Checking Disk Space on AWS EBS"
# The 'df -h' command shows disk space in 'human readable' format (GB/MB)
df -h | grep '^/dev/'
echo "------------------------------------"

echo "STEP 3: Checking Memory (RAM) Usage"
# The 'free -m' command shows memory in Megabytes
free -m
echo "------------------------------------"

echo "--- HEALTH REPORT COMPLETE ---"
```

---

## 4. Saving and Running: The Moment of Truth

Click the **Save** button at the bottom. You are now looking at the "Job Dashboard." This is the control center for this specific task.

### The "Build Now" Button
On the left sidebar, you will see a link that says **Build Now**. Click it.
- **The Event:** Look at the bottom left under **Build History**. You will see a small circle and a number like `#1`.
- **The Animation:** If the circle is "Flashing," it means the robot is currently working.
- **The Result:** After a few seconds, the circle will turn a solid color.
    - **Blue (or Green):** Success! Your script finished perfectly.
    - **Red:** Failure. One of your commands crashed or returned an error.

---

## 5. Console Output: Learning to Read the Logs

As a DevOps engineer, you will spend 50% of your life reading logs. Jenkins provides a "Console Output" for every single build.

1.  Click on the build number (`#1`) in the history.
2.  Click **Console Output** on the left.
3.  **Analysis:**
    - **"Started by user admin":** Jenkins tells you exactly who triggered the work.
    - **"Running as SYSTEM":** This tells you the Linux user account Jenkins is using.
    - **"Building in workspace /var/lib/jenkins/workspace/NAME":** This is the exact folder where your script is running. 
    - **The Output:** You should see the results of your `uptime`, `df`, and `free` commands exactly as if you typed them yourself.
    - **"Finished: SUCCESS":** This is the "Gold Medal." It means every command in your script returned an "Exit Code 0."

---

## 6. Modifying and Tuning: The "Configure" Loop

DevOps is never "One and Done." You are constantly improving your automation.
- Click the **Configure** link on the left to go back to the settings.
- **Exercise:** Try adding a new command to your shell script, like `lscpu` to see the processor information.
- Click **Save** and **Build Now** again. Notice how build `#2` appears in the history. You are now building a "Track Record" of your server's health.

---

## 7. Troubleshooting the "First Build" Disasters

Even a simple job can fail. Here are the three most common reasons and how to fix them like a senior engineer.

### A. The "Command Not Found" Error
- **The Symptom:** You see `bash: line 5: uptme: command not found` in the console logs.
- **The Cause:** You made a typo in your script.
- **The Fix:** Go back to "Configure" and fix the spelling.

### B. The "Permission Denied" Error
- **The Symptom:** You see `Permission denied` when trying to read a file or run a command.
- **The Cause:** Jenkins runs as the `jenkins` user, not as `root`. The `jenkins` user doesn't have permission to look inside certain folders (like `/root`) or run certain commands (like `sudo apt update`).
- **The Fix:** Don't use `sudo` in Jenkins scripts unless you have specifically configured "Passwordless Sudo" for the jenkins user (which is a security risk!).

### C. The "Agent Offline" Error
- **The Symptom:** The build is stuck in "Pending" and never starts.
- **The Cause:** You told the job to run on a specific machine (an Agent), but that machine is turned off in AWS.
- **The Fix:** Check the "Nodes" section in Jenkins to see if your worker machines are "Online."

---

## Summary

-   **Freestyle Jobs** are the perfect starting point for learning the "DNA" of Jenkins.
-   **Naming** should be professional, hyphenated, and easy to search.
-   **Descriptions** and **Build Rotation** are the "Janitorial" duties that keep your server professional and healthy.
-   **Execute Shell** allows you to run any Linux command, making Jenkins a powerful tool for AWS system administration.
-   **Console Output** is your primary tool for debugging. Read every line to understand what the robot is doing.
-   **Troubleshooting** usually comes down to typos, permission issues, or resource limitations on your EC2 instance.

In the next file, `03-job-configuration.md`, we will go even deeper. We will learn how to make our jobs "Smart" by adding parameters, environment variables, and connections to external tools.
