# Installing Jenkins on Windows

## Introduction

Imagine you are a senior DevOps engineer for a massive game development studio. Your developers are building the next world-famous RPG using the **Unreal Engine**. They aren't using Linux. They are using high-end Windows workstations with powerful graphics cards. To build their game, your CI/CD server *must* have access to the Windows SDK, DirectX, and various Microsoft-only libraries.

While 90% of the world runs Jenkins on Linux, there is a very important 10% that **requires Windows.** Whether you are building legacy `.NET Framework` applications, desktop software, or high-end games, you will eventually find yourself needing a Windows-based Jenkins server.

Installing Jenkins on Windows is very different from the Linux world. You don't use `apt` or `yum`. You use a standard Windows Installer (`.msi`). But don't let the "Next, Next, Finish" wizard fool you — there are many hidden traps in the Windows installation that can cause your server to be insecure, slow, or completely unstable.

In this file, we are going to go into **extreme depth** on the Windows installation process. We will talk about service accounts, Java environment variables, the 260-character path limit, and the "Senior Secrets" of managing Jenkins in a Windows-centric AWS environment.

---

## 1. Prerequisites: Preparing the Windows Server

Before you run the installer, you need to prepare the "Body" of your server. In AWS, this means launching an **EC2 Instance running Windows Server 2022.**

### A. Hardware Requirements (The Windows Tax)
Windows is much "heavier" than Linux. It has a graphical interface and many background services that eat RAM before Jenkins even starts.
- **Instance Type:** Minimum `t3.large` (2 vCPUs, 8GB RAM). Do not attempt this on a `t3.medium`.
- **Storage:** Minimum 50GB. Windows and the Jenkins workspace grow incredibly fast.

### B. Installing Java (The First Step)
Jenkins is a Java application. It cannot run without a **Java Development Kit (JDK)**. 
1.  Download the **OpenJDK 17 MSI** from a provider like Adoptium.
2.  Run the installer.
3.  **Crucial Step:** During the installation, ensure you check the boxes for:
    - **"Set JAVA_HOME variable"**
    - **"Add to PATH"**
4.  Open a **PowerShell** window and type `java -version`. If this doesn't work, Jenkins will fail to install.

---

## 2. The MSI Installation Walkthrough

Download the official `jenkins.msi` from the Jenkins website. When you run it, pay close attention to these two screens:

### A. The Installation Path
By default, the installer suggests `C:\Program Files\Jenkins`. 
- **Senior Tip:** **Change this.** Use a path like `C:\Jenkins`. 
- **Why?** Windows permissions in the "Program Files" folder are extremely strict. This can cause problems later when Jenkins tries to update its own plugins or write temporary files. Using a root folder like `C:\Jenkins` makes your life much easier.

### B. Service Logon Credentials (The Security Choice)
This is the most important screen in the wizard. Jenkins runs as a "Windows Service" in the background. You must choose which user account the service will use.

1.  **Run service as LocalSystem (Not Recommended):** This is the "God Mode" account. If Jenkins is hacked, the hacker has total control over your entire Windows server. 
2.  **Run service as a local or domain user (Recommended):** You should create a dedicated Windows user named `jenkins_service`. This way, if Jenkins is compromised, the damage is limited to only that user's folders.

---

## 3. Configuring the Windows Firewall

By default, Windows is a "Locked House." It will block all incoming traffic to port 8080. You won't be able to see the Jenkins UI from your laptop until you open the gate.

### How to open the port:
1.  Search for **"Windows Defender Firewall with Advanced Security"** in the Start menu.
2.  Select **Inbound Rules** on the left.
3.  Click **New Rule** on the right.
4.  Select **Port** → **TCP** → **Specific local ports: 8080**.
5.  Select **Allow the connection**.
6.  Name it `Jenkins-UI`.

**AWS Note:** Remember that you *also* have to open port 8080 in the **AWS Security Group** (the virtual firewall outside the server). You need both gates open to get through!

---

## 4. The 260-Character Path Limit (The Windows Nightmare)

This is the #1 reason Jenkins builds fail on Windows. By default, Windows cannot handle file paths that are longer than 260 characters.

**The Scenario:**
- Jenkins creates a deep folder structure: `C:\Jenkins\workspace\My-Project-Name\Branch-Feature-X\Modules\Submodule-Y\Tests\Result.xml`.
- If this string is longer than 260 characters, Jenkins will crash with a "File not found" error.

**The Senior Fixes:**
1.  **Keep it short:** Install Jenkins at `C:\J` instead of `C:\Jenkins`.
2.  **Registry Hack:** In modern Windows (Server 2016+), you can enable "Long Paths" in the Windows Registry.
    - Path: `HKLM\SYSTEM\CurrentControlSet\Control\FileSystem`
    - Key: `LongPathsEnabled` (Set to `1`).
3.  **Short Names:** Tell your developers to keep their repository and branch names short.

---

## 5. Performance: Antivirus Exclusions

Windows Defender is a great security tool, but it is the enemy of build speed. 

**The Problem:** Every time Jenkins compiles code, it creates thousands of tiny files. Windows Defender will try to scan every single one of those files for viruses. This will make your builds **10 times slower.**

**The Senior Fix:** Add an "Exclusion" for the `C:\Jenkins` folder in Windows Defender. This tells Windows: "I trust this folder, don't scan it." This single change will drastically improve your Jenkins performance on AWS.

---

## 6. Comparison: Windows vs. Linux Management

| Feature | Windows Jenkins | Linux Jenkins |
| :--- | :--- | :--- |
| **Service Manager** | Services.msc | systemd (systemctl) |
| **Config File** | `jenkins.xml` | `/etc/default/jenkins` |
| **Shell** | PowerShell / CMD | Bash / Sh |
| **Permissions** | NTFS / Active Directory | Linux User / Group |
| **Updates** | Run MSI again | `apt upgrade` |
| **Stability** | High (if tuned) | **Extremely High** |

---

## Summary

-   **Windows** is required for specialized workloads like .NET Framework or Game Development (Unreal/Unity).
-   **Java 17** must be installed and the `JAVA_HOME` variable set before Jenkins.
-   **Security:** Always run Jenkins as a dedicated "Service User," never as LocalSystem.
-   **Paths:** The 260-character limit is your biggest enemy on Windows. Keep your folder names short.
-   **Performance:** Exclude your Jenkins directory from Antivirus scanning to avoid massive slowdowns.
-   **Firewall:** Port 8080 must be opened in both Windows and the AWS Security Group.

In the next chapter, `01-first-time-setup.md`, we will leave the operating system behind and start the web-based configuration that turns your raw installation into a working DevOps engine.
