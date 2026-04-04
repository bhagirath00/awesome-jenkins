# Shell Build Steps: The Swiss Army Knife of Jenkins Automation

## 1. Introduction: The Power of the Raw Terminal

Imagine you are a world-class master carpenter. You have a massive workshop filled with every specialized tool imaginable. You have laser-guided saws for perfect cuts, pneumatic hammers for fast nailing, and specialized drills for different types of wood. But if you look at your belt, there is one tool that you use more than any other: a simple, sharp, high-quality **Swiss Army Knife.**

It isn't the most specialized tool. It doesn't have a laser or a motor. But it is the most flexible tool in your entire workshop. It can cut string, it can shave a piece of wood, it can tighten a loose screw, and it can open a bottle of water at the end of the day. Because it is simple and raw, it can do almost anything.

In the world of Jenkins, the **Shell Build Step** is that Swiss Army Knife.

While Jenkins has thousands of specialized "Plugins" for things like Maven, Docker, or AWS, the Shell step allows you to run any raw command that you could normally type into a Linux terminal. If you can do it in a Bash shell, you can do it in Jenkins. This raw power and flexibility make it the #1 choice for senior DevOps engineers who need to build custom automation that doesn't fit into a "Pre-made" plugin box. 

If you are just using plugins, you are a "Consumer" of automation. But if you master the Shell Build Step, you become a "Creator" of automation. In this file, we are going to go into **unprecedented, extreme technical depth** on how to write professional, production-grade shell scripts inside Jenkins. We will talk about the philosophy of shell selection, the intricacies of environment variables, the critical importance of exit codes, and how to build high-security deployment scripts for AWS that are impossible to break.

---

## 2. Why Choose Shell over Specialized Plugins?

A junior engineer might look at the "Docker Build" plugin and the "Execute Shell" box and ask: "Why would I ever write my own shell script when someone already wrote a plugin for me?" This is a fair question, but a senior engineer knows that "Convenience" often comes with a "Cost."

### The Senior Perspective on Flexibility:
1.  **Total Transparency:** With a shell script, you can see every single character of the command being sent to the server. A plugin is a "Black Box." If it fails, it might just give you a generic error message like "Docker Build Failed." With a shell script, you see the exact error from the Docker engine itself.
2.  **Zero "Lock-In":** If you write your deployment logic in a shell script, you can take that script and run it on your local laptop, on GitLab, on GitHub Actions, or on a raw EC2 instance. If you use a specialized Jenkins plugin, your automation is "Trapped" inside Jenkins. If you ever want to move to a different tool, you have to start from scratch.
3.  **Speed of Updates:** Sometimes a new version of the AWS CLI or Docker comes out with a critical new feature. It might take months for the Jenkins plugin developer to update their code to support that feature. But with a shell script, you can use the new feature the **second** you install the new CLI version on your server.
4.  **Complex Logic:** Plugins are great for simple tasks. But what if you need to say: "Only push the Docker image if the current time is between 9 AM and 5 PM, and only if the S3 bucket has less than 100 files in it"? That kind of complex, "Business Logic" is impossible for a plugin, but it's just a simple `if` statement in a shell script.

---

## 3. The Anatomy of the "Execute Shell" Step

When you add an "Execute Shell" step to your Freestyle job, Jenkins creates a temporary script file on the Linux server and executes it. But which "Shell" does it use?

### A. The Shebang (`#!`) - The First Line of Defense
By default, most Jenkins installations use `/bin/sh`. On many modern Linux systems (like Ubuntu), `/bin/sh` is actually a very basic, lightweight shell called **Dash.** While Dash is fast, it is missing many of the powerful features that we need for DevOps.

**The Senior Rule:** Always start your scripts with `#!/bin/bash`. 
- **Bash** (The Bourne Again SHell) is the industry standard. It supports:
    -   **Arrays:** For managing lists of servers or files.
    -   **Advanced String Manipulation:** For parsing version numbers.
    -   **Process Substitution:** For complex piping of data.

### B. Environment Variables: The "Context" of the Build
Jenkins doesn't run your script in a vacuum. It "Injects" a massive amount of information into the shell as environment variables. You must learn to use these to make your scripts "Aware" of where they are.

| Variable Name | What it tells the Script | Example Usage in Code |
| :--- | :--- | :--- |
| `${JOB_NAME}` | The name of the project. | `echo "Report for ${JOB_NAME}"` |
| `${BUILD_NUMBER}` | The current run (1, 2, 3...). | `IMAGE_TAG="v${BUILD_NUMBER}"` |
| `${WORKSPACE}` | The folder where the code is. | `cd ${WORKSPACE}/src/app` |
| `${GIT_COMMIT}` | The long ID of the code. | `echo "Built from commit ${GIT_COMMIT}"` |
| `${NODE_NAME}` | Which AWS server is running this? | `echo "Running on agent ${NODE_NAME}"` |
| `${BUILD_URL}` | A link back to the Jenkins UI. | `echo "View logs here: ${BUILD_URL}"` |

---

## 4. Exit Codes: The "Language" of Success and Failure

How does Jenkins know if your build passed or failed? It doesn't "Read" your text. It looks at the **Exit Code** of the shell process.

### The Number of Destiny:
-   **Exit Code 0:** Means "SUCCESS." Jenkins marks the step as blue and moves to the next one.
-   **Exit Code 1 to 255:** Means "FAILURE." Jenkins marks the step as red and stops the entire job immediately.

### The "Silent Failure" Nightmare
This is the most dangerous trap in all of DevOps. Imagine this script:
```bash
#!/bin/bash
# Step 1: Delete old logs
rm -rf /tmp/logs
# Step 2: Copy new data (THIS FAILS because the disk is full)
cp /data/new.txt /tmp/logs/
# Step 3: Print a message
echo "Process finished successfully!"
```
In this script, the `cp` command fails. But the `echo` command (the last line) succeeds. Because the **last line** succeeded, the whole script returns an exit code of `0`. **Jenkins will think your build passed even though your data was never copied!** This is how production databases get corrupted.

### The Senior Solution: `set -e` and `set -o pipefail`
A professional DevOps engineer **ALWAYS** starts their scripts with these three lines:
1.  `#!/bin/bash`
2.  `set -e`: "Stop immediately if any command fails."
3.  `set -x`: "Print every command to the console (for debugging)."
4.  `set -o pipefail`: "If I pipe two commands together (`A | B`), and A fails but B succeeds, the whole thing should fail."

---

## 5. Practical Example: A Production Docker Build Script

Let's look at a "Real-World" script. This is what you would write to build a Docker image for a Java application and push it to **Amazon ECR (Elastic Container Registry).** Notice how we use variables and error handling to make it professional.

```bash
#!/bin/bash
# -------------------------------------------------------------------------
# SCRIPT: build-and-push-to-ecr.sh
# PURPOSE: Builds a Docker container and pushes to AWS
# -------------------------------------------------------------------------
set -e # Exit on error
set -x # Print commands

# 1. Define our AWS and Project variables
# Using variables makes the script easy to update later
AWS_REGION="us-east-1"
ACCOUNT_ID="123456789012" # Replace with your real AWS ID
PROJECT_NAME="checkout-service"
IMAGE_TAG="v${BUILD_NUMBER}" # Using the Jenkins build number as the version
ECR_URL="${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
FULL_IMAGE_NAME="${ECR_URL}/${PROJECT_NAME}:${IMAGE_TAG}"

echo "--- STARTING BUILD FOR ${PROJECT_NAME} ---"

# 2. Login to Amazon ECR
# We use the AWS CLI to get a temporary password and pipe it to Docker
echo "Step 1: Logging into AWS..."
aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_URL}

# 3. Build the Docker Image
# We use the '.' to look for the 'Dockerfile' in the current workspace
echo "Step 2: Building the container image..."
docker build -t ${PROJECT_NAME} .

# 4. Tag the image for the ECR repository
# We create a specific version tag AND a 'latest' tag
echo "Step 3: Tagging the image..."
docker tag ${PROJECT_NAME} ${FULL_IMAGE_NAME}
docker tag ${PROJECT_NAME} ${ECR_URL}/${PROJECT_NAME}:latest

# 5. Push the images to the cloud
echo "Step 4: Pushing to Amazon ECR..."
docker push ${FULL_IMAGE_NAME}
docker push ${ECR_URL}/${PROJECT_NAME}:latest

echo "--- SUCCESS: ${PROJECT_NAME} VERSION ${IMAGE_TAG} IS NOW LIVE ---"
```

---

## 6. Security: The Secret of Masking

As a senior engineer, you must protect your company's secrets. 
- **The Sin:** Never hardcode your AWS Access Key or your Database Password in the "Execute Shell" box.
- **The Solution:** Use **Jenkins Credentials**. 
    - You store the password in the Jenkins vault.
    - You tell the job to "Bind" that secret to an environment variable (e.g., `DB_PASS`).
    - In your shell script, you use `${DB_PASS}`.
- **The Magic:** Jenkins will look at your console logs. If it sees the value of your password being printed, it will automatically replace it with `****`. This is called **Log Masking**, and it is a mandatory security practice for every senior DevOps engineer.

---

## 7. Summary: The Shell Scripting Checklist

Before you save a shell build step, ask yourself these five questions:
1.  **Does it have a shebang?** (`#!/bin/bash`)
2.  **Is `set -e` enabled?** (To prevent silent failures).
3.  **Are you using variables?** (To avoid hardcoding paths and IDs).
4.  **Are you using the Jenkins Credentials vault?** (For all passwords).
5.  **Is it "Idempotent"?** (If you run the script twice, will it work correctly the second time, or will it crash because a folder already exists?).

If you follow these rules, your Jenkins server will be as stable and secure as the biggest tech companies in the world.

---

## Summary

-   **Shell Build Steps** provide the raw flexibility needed for custom DevOps automation.
-   **Transparency and Portability** make Shell a superior choice over many specialized plugins.
-   **Environment Variables** provide the "Building Blocks" for dynamic, aware scripts.
-   **Exit Codes** are the heart of build reporting—ignore them at your peril.
-   **Error Handling (`set -e`)** is the difference between a "Junior" script and a "Production" script.
-   **AWS Integration:** Shell scripts are the best way to interact with the AWS CLI for Docker and S3 tasks.
-   **Security:** Masking and Credentials prevent your company's secrets from being leaked in the logs.

In the next file, `02-windows-batch-steps.md`, we will learn how to perform these same professional tasks in a Windows environment using Batch and PowerShell.
