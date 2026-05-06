# Authentication Methods: Proving Your Identity

## 1. Introduction: The Passport Control

Imagine you are traveling to a **Foreign Country**.
As you step off the airplane, you are funneled into a long line at
**Passport Control**.
At the end of the line, there is a specialized officer sitting behind
a glass window.
The officer doesn't know you. They don't care if you are a nice person
or if you have a lot of money.
They only care about one thing: **Identity**.

You hand them your **Passport**.
The passport is a "Trusted Document" issued by a government.
It has your photo, it has a unique number, and it has security
features that are impossible to fake.
The officer scans the passport and compares it to their database.
If the passport is valid, the officer says "Welcome" and lets you
into the country.
This is **Authentication**.

In the world of Jenkins, **Authentication Methods** are those passports.
They are the protocols and systems that Jenkins uses to verify that
the person trying to log in is truly who they say they are.
Without strong authentication, anyone could walk into your "Country"
and take control of your AWS infrastructure.

## 2. Choosing the Right "Security Realm"

In Jenkins, the source of your users is called the **Security Realm**.
A senior DevOps engineer chooses the realm based on the size and
security requirements of the company.

### Realm 1: Jenkins' Own Database
Jenkins stores the usernames and passwords in its own internal file.
-   **Best For:** Small teams or personal projects.
-   **Senior Warning:** Never use this for a large company! It is a
    nightmare to manage 500 individual passwords in Jenkins.

### Realm 2: LDAP / Active Directory
Jenkins connects to your company's existing "Corporate Phonebook."
-   **Best For:** Large enterprises.
-   **Benefit:** If an employee leaves the company and is deleted
    from the phonebook, their Jenkins access is revoked automatically.

### Realm 3: GitHub / GitLab OAuth
Jenkins uses your Git account to verify your identity.
-   **Best For:** Modern, remote teams.
-   **Benefit:** It's very convenient. If you have a Git account,
    you have a Jenkins account.

---

## 3. Machine-to-Machine Authentication

It's not just humans that need to log into Jenkins.
Sometimes, an **AWS Script** or a **GitHub Webhook** needs to talk
to Jenkins.
We don't give these machines a password. Instead, we use:
-   **API Tokens:** A long, random string that acts as a permanent
    key for a specific task.
-   **SSH Keys:** For secure, automated connections between servers.

---

## Summary

- Authentication is the "passport control" process that verifies a user's identity
- The Security Realm defines where user data is stored and how it is verified
- Jenkins' internal database is suitable for small projects but lacks enterprise scalability
- LDAP and Active Directory integration is the professional standard for large organizations
- OAuth (e.g., GitHub login) provides a modern and convenient authentication experience
- API Tokens allow machines and scripts to authenticate securely without using passwords
- Strong authentication is the first and most critical layer of defense for Jenkins on AWS

In the next file, `03-authorization-strategies.md`, you will learn how to control what those users can do once they have entered the "Country."
