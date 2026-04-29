# Email Notifications: The Official Record

## 1. Introduction: The Registered Letter

Imagine you are a **Business Owner** who is signing a contract to
purchase a new warehouse.
The negotiation was done over the phone. The details were discussed
in person.
But for the deal to be "Real," you need a **Registered Letter**.
You need a physical document that is delivered to your office,
signed by a courier, and placed in your permanent file.
This letter is the "Official Record" of the transaction.
It doesn't happen "Instantly" like a text message, but it is much
more important and much more durable.

In the world of Jenkins, **Email Notifications** are that registered
letter.
While Slack is for "Instant Action," Email is for "Long-Term Records."
Email provides a permanent, searchable, and formal history of every
major event in your pipeline's lifecycle.

## 2. Why use Email? (The Compliance Bridge)

A senior DevOps engineer uses Email for "Formal Reporting" and
"Stakeholder Communication."

### Key Use Cases:
-   **Release Reports:** Sending a "Deployment Summary" to the
    management team every Friday.
-   **Security Audits:** Sending a daily report of all "Failed Login
    Attempts" on the Jenkins controller.
-   **Formal Approvals:** Sending a "Request for Signature" to a
    compliance officer before a high-stakes AWS deployment.
-   **Long-Term Archive:** If your Slack history is deleted after
    90 days, your Email history stays in your inbox forever.

---

## 3. The "Email-ext" Plugin

Jenkins has a basic email tool, but a professional engineer uses
the **"Email Extension" (Email-ext) Plugin**.

### Why?
Because the Email-ext plugin allows you to build **HTML Templates**.
Instead of a boring text message like "Build #42 failed," you can
send a beautiful, branded email that contains:
1.  A "Success/Failure" banner.
2.  A list of the latest Git commits.
3.  A summary of the test results.
4.  Clickable links to the AWS console for the new resources.

This "Professionalism" helps build trust between the DevOps team
and the rest of the business.

---

## Summary

- Email notifications serve as the "official record" for long-term tracking and compliance
- While Slack is for instant action, Email is for formal reporting to stakeholders and managers
- The Email-ext plugin allows for rich HTML templates that provide a professional build summary
- Automated reports can include test summaries, commit histories, and direct links to AWS
- Email provides a durable archive of pipeline history that is easily searchable for audits
- Senior engineers use email for high-stakes approvals and weekly performance summaries
- Combining Slack and Email creates a "dual-layered" communication strategy for the enterprise

In the next file, `03-teams-notifications.md`, you will learn how to integrate Jenkins with the Microsoft ecosystem.
