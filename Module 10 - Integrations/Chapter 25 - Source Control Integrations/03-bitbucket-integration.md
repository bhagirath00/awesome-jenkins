# Bitbucket Integration: The Professional Archives

## 1. Introduction: The Library and the Research Center

Imagine you are a **Senior Researcher** at a world-class university.
Your work involves analyzing ancient manuscripts (the **Source Code**).
These manuscripts are stored in a high-security, climate-controlled
**Library** (the **Bitbucket Server**).
The library is a place of "Order" and "Standardization." Every book
is categorized, every room is silent, and every visitor is tracked.

But the library is not where the "Analysis" happens.
The library is for "Storage."
To do your research, you take a copy of the manuscript to the
**Research Center** (the **Jenkins Server**).
The research center is full of high-tech machines, microscopes,
and chemical scanners.
It is where the "Heavy Lifting" of the analysis takes place.

For your research to be successful, the library and the research center
must have a **Seamless Connection**.
When a new manuscript arrives at the library, the research center
must be notified instantly.
When the research center discovers a fact about the manuscript,
that fact must be "Stamped" onto the original book in the library.

In the world of Enterprise DevOps, **Bitbucket Integration** is that
connection.
Bitbucket (especially the "Data Center" version) is the standard for
large, regulated industries like Banking and Insurance.
The integration ensures that your "Archives" stay perfectly synced
with your "Automation."

## 2. The Features of the Bitbucket Plugin

A senior DevOps engineer uses Bitbucket integration to create a
"Traceable Workflow."

### Key Features:
-   **Commit Status Reporting:** Jenkins reports the success or
    failure of a build directly on the Bitbucket "Commits" page.
-   **Branch Discovery:** The plugin can automatically "Scan" your
    Bitbucket project and create a Jenkins job for every repository
    it finds.
-   **Atlassian "Smart Commits":** You can include commands in your
    Git commit message (like `#build`) to specifically tell Jenkins
    what to do.

---

## 3. The Connection Protocol

To connect Jenkins to Bitbucket, you use the **Bitbucket Branch
Source** plugin.

1.  **Application Link:** For on-premise servers, you create a trusted
    "Application Link" using a certificate.
2.  **App Password:** For Bitbucket Cloud, you generate a specialized
    "App Password" for Jenkins.
3.  **Webhook:** You tell Bitbucket to send a "Post-Receive" notification
    to the Jenkins server.

This ensures that the library and the research center are always
in perfect synchronization.

---

## Summary

- Bitbucket integration provides a high-security link between enterprise archives and automation
- Jenkins reports real-time build statuses directly onto the Bitbucket commits and branches
- Automated branch discovery allows Jenkins to self-configure based on Bitbucket repository lists
- "Smart Commits" enable developers to trigger specific build behaviors via Git commit messages
- The Bitbucket Branch Source plugin is the enterprise standard for Atlassian-based workflows
- Secure authentication is achieved through Application Links (on-prem) or App Passwords (cloud)
- This integration is favored by regulated industries for its order, traceability, and security

In the next file, `04-multibranch-pipeline.md`, you will learn how to manage multiple branches across all these platforms.
