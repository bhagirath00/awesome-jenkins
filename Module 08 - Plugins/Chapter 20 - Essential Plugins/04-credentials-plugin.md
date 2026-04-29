# The Credentials Plugin: The High-Security Vault

## 1. Introduction: The Bank Vault and the Safety Deposit Box

Imagine you are the manager of a **High-Security Bank**.
In your bank, there are thousands of customers who have very
valuable items—gold, jewelry, and top-secret documents.
These items are the "Keys" to their lives.
If these items are stolen, the customers lose everything.

You don't just leave these items sitting on a table in the lobby.
Instead, you have a **Giant Steel Vault**.
The vault is hidden behind a thick door that requires multiple
keys to open.
Inside the vault, every customer has their own **Safety Deposit Box**.
The customer gives you their item, you put it in the box, and you
lock it.
When the customer needs the item, they come to the bank, prove
their identity, and you bring the item to them in a "Private Room."
Once they are finished, the item goes back into the vault.

In the world of DevOps, **The Credentials Plugin** is that bank vault.
The "Valuable Items" are your **Passwords**, **AWS Keys**, and
**Git Tokens**.
The "Private Room" is your **Jenkins Pipeline**.
The credentials plugin ensures that your secrets are never "Seen" by
anyone, and they are never stored in your source code.

## 2. Why Secrets Management is Critical

A senior DevOps engineer knows the #1 rule of security:
**Never put a password in a Jenkinsfile or a script.**

### The Risk of the "Plain Text" Secret:
If you put your AWS access key directly into your code, and you
push that code to GitHub, your company's AWS account could be
hacked in seconds.
Robots are constantly "Scanning" GitHub for secrets.
By using the Credentials Plugin, you "Abstract" the secret.
Your code doesn't say "The password is '12345'."
Instead, your code says "Go to the vault and get the item labeled
'aws-prod-key'."

---

## 3. Types of Credentials

The vault can store many different types of "Items":

-   **Username and Password:** For basic logins.
-   **Secret Text:** For API tokens (like Slack or GitHub tokens).
-   **Secret File:** For sensitive configuration files or certificates.
-   **SSH Private Key:** For logging into AWS EC2 instances securely.
-   **AWS Credentials:** Specialized storage for Access Keys and Secret
    Keys.

---

## Summary

- The Credentials Plugin acts as a high-security vault for passwords, tokens, and AWS keys
- It prevents sensitive data from being exposed in source code or Jenkins logs (masking)
- "Abstraction" allows pipelines to request a secret by its ID rather than its actual value
- The plugin supports multiple credential types, including SSH keys, API tokens, and secret files
- Scoping allows you to restrict secrets to specific folders or projects for improved security
- Using this plugin is a mandatory requirement for maintaining compliance and infrastructure safety
- A senior engineer treats the Credentials vault as the "single source of truth" for all secrets

In the next file, `05-blue-ocean-plugin.md`, you will learn about the modern, visual interface for Jenkins.
