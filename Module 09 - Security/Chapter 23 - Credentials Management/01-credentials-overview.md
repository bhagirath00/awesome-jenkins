# Credentials Overview: The High-Security Vault

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

In the world of DevOps, **Credentials Management** is that bank vault.
The "Valuable Items" are your **Passwords**, **AWS Keys**, and
**Git Tokens**.
The "Private Room" is your **Jenkins Pipeline**.
The credentials system ensures that your secrets are never "Seen" by
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

## 3. The Credentials Architecture

Jenkins uses a "Layered" approach to secrets.

1.  **Storage:** The secrets are encrypted on the Jenkins disk using
    the "Master Key."
2.  **Mapping:** You give the secret a "User-Friendly ID" (e.g.,
    `github-auth`).
3.  **Injection:** You use that ID in your code, and Jenkins
    automatically "Unlocks" the secret and injects it into your build
    at runtime.

This system provides a perfect "Separation of Concerns" between the
DevOps engineer (who knows the code) and the Security Officer (who
knows the secrets).

---

## Summary

- Credentials management provides a high-security "vault" for protecting enterprise secrets
- Storing passwords or AWS keys in source code is a critical security failure (Secret Leak)
- The system encrypts all stored credentials on the disk using a master security key
- Abstraction allows engineers to reference secrets by a unique ID instead of their actual value
- Injection ensures that secrets are only available to the pipeline during the execution phase
- Separating code from credentials is a mandatory requirement for compliance and safety
- A senior engineer treats the Jenkins vault as the single source of truth for all pipeline secrets

In the next file, `02-adding-credentials.md`, you will learn how to populate the vault with different types of "Valuable Items."
