# Adding Credentials: Populating the Vault

## 1. Introduction: The Customs Officer

Imagine you are a **Customs Officer** at an international shipping
port.
Every day, thousands of containers arrive at your port.
But these are not normal containers. These are **Sealed Vaults**.
Your job is to "Ingest" these vaults into the port's high-security
storage area.

You don't just take the vault and put it on a shelf.
First, you ask the owner: "What is inside this vault?"
Is it a key? Is it a letter? Is it a file?
Then, you give the vault a **Unique Label** so you can find it later.
Finally, you decide "Who" is allowed to see the contents of the
vault.
Is it for Everyone? Is it only for the Ship's Captain?
Is it only for the Port's Manager?

In the world of Jenkins, **Adding Credentials** is that ingestion
process.
You are taking a sensitive piece of data from the real world and
placing it safely into the Jenkins vault.
You must choose the "Correct Type" and the "Correct Scope" for every
secret to ensure it is handled with the proper level of care.

## 2. Choosing the "Right Type"

Different secrets require different "Containers."
In the Jenkins UI, you must select the correct "Kind" of credential.

-   **Username and Password:** The most common type. Used for
    logging into external services like Docker Hub.
-   **Secret Text:** A single, long string. Used for API tokens like
    your GitHub "Personal Access Token."
-   **Secret File:** A whole file that is encrypted. Perfect for
    storing **Kubeconfig** files or security certificates.
-   **SSH Private Key:** Used for secure connections to AWS EC2
    instances.
-   **AWS Credentials:** A specialized type for storing Access Keys
    and Secret Keys together.

---

## 3. The Power of the "ID"

When you add a credential, you must give it an **ID**.
A senior engineer uses a clear, standardized naming convention.
-   `aws-prod-deployer-key`
-   `github-org-readonly-token`

### Why is the ID important?
Because the ID is the **only thing** your code will ever see.
By using a consistent ID, you can change the actual password in the
vault (during a "Password Rotation") without ever having to touch
your pipeline code.
The code just says "Get me the key labeled `aws-prod-key`," and
Jenkins provides the latest version.

---

## Summary

- Adding credentials involves selecting the correct "kind" and "label" for every secret
- Common credential types include username/password, secret text, secret files, and SSH keys
- Specialized AWS credentials allow for the secure storage of access and secret keys together
- The unique "ID" is the handle used by pipeline code to reference the secret in the vault
- Using standardized ID names allows for password rotation without modifying the build code
- Choosing the right credential type ensures that Jenkins handles and masks the data correctly
- Proper ingestion of secrets is the foundation for secure, automated AWS infrastructure

In the next file, `03-credentials-in-pipelines.md`, you will learn the exact syntax for "Unlocking" these secrets in your code.
