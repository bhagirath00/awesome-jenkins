# Windows Build Steps: Mastering Batch and PowerShell

## 1. Introduction: The World of Windows Automation

Imagine you are a specialized construction contractor who has spent your entire 20-year career working exclusively with wood. You know every type of timber, every saw blade, and every specialized glue. You are the master of the "Wooden World." But one morning, you arrive at the job site and discover that the client has decided to build the entire structure out of **Solid Steel.** 

Your old tools—your wood saws and your chisels—still exist in your truck. But they are useless against the steel. The rules of physics and gravity are the same (you still need a foundation and a roof), but the way you join pieces together, the way you measure for errors, and the speed at which you work are completely different.

In the world of Jenkins, **Windows is that steel.**

While the vast majority of the DevOps world lives in the "Wooden World" of Linux and Bash scripts, a significant and critical portion of the global economy runs on Windows. Whether you are managing a massive suite of legacy `.NET Framework` applications, building high-end games in the Unity engine, or managing a fleet of Windows-based AWS EC2 instances for a financial corporation, you must know how to speak the "Language of the Machine" on Windows.

In Jenkins, we have two primary "Dialects" for running commands on Windows agents: **Execute Windows batch command** and **PowerShell**. As a senior DevOps engineer, you cannot just "get by" with a few copy-pasted commands. You must understand the deep architectural differences between these two shells, how they communicate success or failure back to Jenkins, and how to navigate the common pitfalls of the Windows environment that would otherwise bring your CI/CD pipelines to a grinding halt.

---

## 2. Batch vs. PowerShell: The Generational Divide

When you click the "Add build step" button on a Jenkins agent running Windows, you are presented with two choices. Choosing the right one is the difference between a modern, resilient pipeline and a fragile, "old-school" script that will break at the first sign of trouble.

### A. Execute Windows Batch Command (`cmd.exe`)
This is the "Legacy" way of working. It is the direct descendant of the MS-DOS environment from the 1980s.
-   **The Philosophy:** It is a "Command Interpreter." It takes a simple string of text and tells the operating system to execute a specific program.
-   **The Good:** It is incredibly fast to start and uses almost zero RAM. Every Windows machine in existence supports it perfectly.
-   **The Bad:** It is extremely primitive. It has no native support for modern data formats like JSON or XML. It has very weak support for loops and "If" statements. It feels like trying to build a spaceship using only a hammer and a rock.

### B. PowerShell (`powershell.exe`)
This is the modern, "Senior" standard for all Windows-based automation.
-   **The Philosophy:** It is an "Object-Oriented Shell." When you run a command to get a list of files, PowerShell doesn't just give you a list of names (text). It gives you a collection of **File Objects**. You can then ask those objects for their size, their owner, or their last modified date without doing any "String Scraping."
-   **The Good:** It is incredibly powerful. It has built-in support for everything a DevOps engineer needs—from calling REST APIs and parsing JSON to interacting with the Windows Registry and managing AWS resources via the AWS Tools for PowerShell.
-   **The Bad:** It takes slightly longer to start than Batch (milliseconds) and uses more memory.

### Comparison Table: Which Shell to Use?

| Feature | Windows Batch (cmd.exe) | PowerShell (pwsh.exe) |
| :--- | :--- | :--- |
| **Data Model** | String-based (just text). | **Object-based** (actual data). |
| **Error Handling** | Primitive (`ERRORLEVEL`). | **Advanced** (Exceptions/Preference). |
| **JSON Support** | None. You need external tools. | **Native** (`ConvertFrom-Json`). |
| **REST API Support** | None. | **Native** (`Invoke-RestMethod`). |
| **Readability** | Poor (spaghetti code). | **Excellent** (Verbose & clear). |
| **AWS Integration** | Limited to AWS CLI. | **Deep Integration** (AWS Modules). |
| **Senior Choice** | Avoid unless for one-liners. | **The Professional Default.** |

---

## 3. The "Error Level" Trap: Handling Failure on Windows

This is the most important section of this file. In Linux, we use `set -e` to make the script stop when a command fails. On Windows, the behavior is much more dangerous.

### The Batch Nightmare
In a Windows Batch script, if a command fails, the script **does not stop.** It simply continues to the next line. 
```batch
copy c:\my_app\non_existent_file.txt d:\backup\
echo "Backup was a success!"
```
In this example, the `copy` command will fail because the file doesn't exist. But the `echo` command will succeed. Because the **last command** in the script was successful, Jenkins will mark the entire build as **SUCCESS (Blue)**. You might go home at 5 PM thinking your database was backed up, when in reality, the backup failed and you have lost everything.

### The Senior Fix for Batch:
You must manually check the "Error Level" after **every single command** that matters.
```batch
copy c:\app\build.zip d:\deploy\
if %ERRORLEVEL% NEQ 0 (
    echo "CRITICAL ERROR: Copy failed with code %ERRORLEVEL%"
    exit /b 1
)
```

### The PowerShell Solution:
In PowerShell, we have a much more elegant solution. We can tell the entire shell to "Stop" the moment any command has a problem.
```powershell
# This single line is the most important line in Windows DevOps
$ErrorActionPreference = "Stop"

# Now, if this command fails, the script dies and Jenkins shows a RED failure
Copy-Item -Path "C:\source\code.zip" -Destination "D:\destination\"
Write-Host "This line will only run if the copy succeeded!"
```
**Always** use `$ErrorActionPreference = "Stop"` at the top of every Jenkins PowerShell build step.

---

## 4. Environment Variables: The Syntax Shift

Jenkins provides the same information (Job Name, Build Number, etc.) to Windows, but the "Secret Code" to access them is different depending on which shell you use.

-   **Linux (Bash):** `${JOB_NAME}`
-   **Windows (Batch):** `%JOB_NAME%`
-   **Windows (PowerShell):** `$env:JOB_NAME`

### Practical AWS Environment Variable Table:

| Variable Name | Role in the Build | PowerShell Syntax |
| :--- | :--- | :--- |
| **WORKSPACE** | Where the code is. | `$env:WORKSPACE` |
| **BUILD_NUMBER** | The unique run ID. | `$env:BUILD_NUMBER` |
| **AWS_REGION** | Which AWS data center. | `$env:AWS_REGION` |
| **S3_BUCKET** | Where to upload. | `$env:S3_BUCKET` |

---

## 5. Practical Example: A Professional AWS S3 Deployment

Let's look at a "Senior-Grade" PowerShell script. This script doesn't just "run"; it performs checks, handles errors, and provides a professional report in the Jenkins logs. We are syncing a build artifact to an **Amazon S3 Bucket**.

```powershell
# -------------------------------------------------------------------------
# SCRIPT: Deploy-To-S3.ps1
# PURPOSE: Validates build and syncs to AWS S3
# -------------------------------------------------------------------------
$ErrorActionPreference = "Stop" # Kill the script on ANY error

# 1. Define our environment settings
$TargetBucket = "techcorp-production-assets"
$LocalPath = "$env:WORKSPACE\dist"
$Version = "$env:BUILD_NUMBER"

Write-Host "--- DEPLOYMENT INITIALIZED FOR VERSION $Version ---"

# 2. Safety Check: Does the build folder actually exist?
# A junior script would just try to sync and fail silently.
if (-not (Test-Path -Path $LocalPath)) {
    Write-Error "CRITICAL: The build folder $LocalPath was not found! Did the compilation fail?"
    exit 1
}

# 3. Perform the Sync using the AWS CLI
# We use the '--delete' flag to ensure the S3 bucket matches the local folder exactly.
Write-Host "Syncing local dist to S3://$TargetBucket..."
aws s3 sync $LocalPath "s3://$TargetBucket" --region us-east-1 --delete

# 4. Success Reporting
Write-Host "---------------------------------------------------"
Write-Host "SUCCESS: Deployment of Version $Version complete."
Write-Host "---------------------------------------------------"
```

---

## 6. Senior Tips for Windows Jenkins

### A. The "260 Character" Path Limit
Windows has a legacy limit where file paths cannot be longer than 260 characters. 
-   **The Problem:** Jenkins builds deep folders like `C:\Jenkins\workspace\PROJECT\BRANCH\SOURCE\MODULES...`. 
-   **The Fix:** Install Jenkins at the root of the drive (e.g., `C:\J`) and tell your developers to keep their repository and branch names short.

### B. Antivirus Interference
Windows Defender will try to scan every single file Jenkins creates during a build. This will make your builds **extremely slow.**
-   **The Fix:** Add an "Exclusion" for the Jenkins workspace folder in Windows Defender. This one change can save you 15 minutes per build!

---

## Summary

-   **Windows Batch** is a legacy tool for simple tasks. Avoid it for complex logic.
-   **PowerShell** is the industry standard for professional Windows DevOps.
-   **Error Handling** is manual in Batch but can be automated in PowerShell with `$ErrorActionPreference`.
-   **Syntax:** Remember that Windows uses `$env:VAR` instead of `${VAR}`.
-   **Performance:** Path management and Antivirus exclusions are the keys to a fast Windows Jenkins server.

In the next file, `03-invoking-ant-maven.md`, we will leave the raw shells behind and look at the specialized "Build Engines" that Jenkins uses to compile and package Java applications.
