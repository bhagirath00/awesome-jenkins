# Credentials Best Practices: The Senior Rules

## 1. Introduction: The Master Locksmith

Imagine you are a **Master Locksmith** in a massive city.
You are the one who designs the security systems for the banks,
the government buildings, and the multi-million dollar estates.
You don't just "Give people keys."
You have a "System" for managing those keys.

You know that a key is only useful if it is **Kept Safe**.
You know that if a key is lost, the lock must be changed immediately.
You know that different people need different keys—the janitor
doesn't need the key to the vault, and the CEO doesn't need the key
to the supply closet.
And most importantly, you know that keys should be **Rotated** regularly
to ensure that if a thief somehow got a copy, it would eventually
stop working.

In the world of Jenkins and AWS, you are that **Master Locksmith**.
In this final file of Chapter 23, we are going to list the **Senior Rules**
for managing your Jenkins credentials vault like a professional
architect.

## 2. Rule 1: Use the "Principle of Least Privilege"

As we learned in Chapter 22, never give an automation service more
power than it needs.
-   **Bad Practice:** Giving Jenkins a "Full Administrator" AWS Key.
-   **Senior Practice:** Creating a specialized IAM User with a
    "Read-Only" or "S3-Only" policy and giving **that** key to Jenkins.
This ensures that if your Jenkins server is hacked, the attacker
can only see your files, not delete your entire company's infrastructure.

## 3. Rule 2: Implement "Scoping" and "Folders"

Don't put all your secrets in one giant pile.
Jenkins allows you to create **Folders** for different projects.
-   **Senior Strategy:** You put the "Project A" secrets inside the
    "Project A" folder.
-   **The Benefit:** Now, the developers working on "Project B" cannot
    even see that the "Project A" secrets exist. This "Isolation"
    is the foundation of enterprise security.

## 4. Rule 3: Rotate Your Secrets Regularly

A secret is like a **Bottle of Milk**—it has an "Expiration Date."
-   **Senior Strategy:** Every 90 days, you generate a new AWS Access
    Key, update the Jenkins vault, and delete the old key.
-   **The Benefit:** If an old key was accidentally leaked or stolen
    months ago, it will eventually become "Dead" and useless to
    the thief.

---

## Summary

- The Principle of Least Privilege limits the impact of a security breach on AWS resources
- Scoping secrets to specific folders ensures that project teams can only access their own data
- Regular secret rotation is a mandatory senior practice for minimizing the risk of leaked keys
- Specialized IAM policies should be used instead of full administrator access for Jenkins
- "Folders" in Jenkins provide a secure way to isolate and manage project-specific credentials
- Storing permanent data in the vault prevents the need for manual, error-prone credential entry
- Professional credentials management is a continuous process of auditing, rotation, and isolation

In the next chapter, **Chapter 24: Advanced Security**, you will learn how to harden the Jenkins controller itself against the world.
