# Master-Agent Architecture

## Introduction

In the last file, we learned about the "Chef and the Cooks" (the Controller and the Agents). But we didn't talk about how the Chef actually talks to the Cooks. If the Chef is in the office and the Cook is in the kitchen, how do they communicate? Do they use a walkie-talkie? A megaphone? A phone call?

In the world of Jenkins, this is called the **Communication Protocol.** 

Understanding how the Controller and Agents talk to each other is critical for security and reliability. If the connection is slow, your builds will take longer. If the connection is insecure, someone could listen in on your AWS keys. In this file, we are going to dive deep into the two main ways Jenkins connects its cluster together.

---

## The Two Directions of Communication

There are two main ways for a "Chef" and a "Cook" to start a conversation.

### 1. The Controller Calls the Agent (Outbound SSH)
In this model, the Controller initiates the connection. 
- **The Story:** Imagine the Chef walkies into the kitchen and says, "Hey Cook, I'm here. Start working." 
- **How it works:** The Jenkins Controller uses the **SSH (Secure Shell)** protocol to log into the Agent machine. 
- **Best for:** Linux machines. Since almost every Linux server in AWS comes with SSH pre-installed, this is the standard way we manage Linux agents. You just give Jenkins the Private Key (`.pem` file), and it handles the rest.

### 2. The Agent Calls the Controller (Inbound Agents)
In this model, the Agent initiates the connection.
- **The Story:** Imagine a new cook walks into the kitchen and says, "Chef, I'm here and ready to work. What do you have for me?"
- **How it works:** A small piece of software (the Jenkins Agent jar) runs on the Agent machine. It reaches out to the Jenkins Controller's URL over a specific port (usually port 50000).
- **Best for:** Windows machines or machines behind a firewall. If your Jenkins Controller is in AWS, but your Agent is on your local laptop, the Controller cannot "see" your laptop to start an SSH connection. But your laptop can "see" the Controller's public URL.

---

## Comparison of Connection Protocols

| Protocol | Direction | Common Usage | Security Method |
| :--- | :--- | :--- | :--- |
| **SSH** | Controller → Agent | Linux EC2 Instances | SSH Keys (Private/Public) |
| **Inbound (JNLP)** | Agent → Controller | Windows, Local Laptops | Secret Tokens |
| **WebSocket** | Agent → Controller | Modern web-friendly Inbound | Secret Tokens over HTTPS |

---

## Security: The "Vault" Between Machines

When the Controller and Agent are talking, they are often passing sensitive information. If you are building a Docker image, the Controller might send the Agent your **AWS Access Keys** so it can push to ECR.

If that conversation isn't encrypted, a hacker on the network could steal those keys.

### 1. SSH Encryption
When using SSH, the entire conversation is encrypted using high-grade security. Even if someone intercepts the data, they will just see gibberish. This is why we almost always use SSH for Linux nodes in production.

### 2. IAM Instance Profiles (The AWS Gold Standard)
If your Jenkins Agent is running on an AWS EC2 instance, you should **never** pass raw access keys over the network. Instead, we use **IAM Roles.**

- You create an IAM Role in AWS called `Jenkins-Agent-Role`.
- You attach a policy to it that allows it to talk to S3 and ECR.
- You attach that role directly to the EC2 instance.
- Jenkins doesn't have to "send" any keys. The Agent machine just *has* the permission because of the role it was born with. This is the most secure way to handle AWS in a Jenkins architecture.

---

## The "Remoting" Jar: The Language of Jenkins

When the connection is established (whether by SSH or Inbound), the Controller sends a small file to the Agent called `remoting.jar`.

Think of this like a **Translator.** 
The Controller speaks "Jenkins Language," and the Agent machine speaks "Linux" or "Windows." The `remoting.jar` sits in the middle and translates the Jenkins commands into actions the machine understands. 

If this "Translator" crashes, the Agent goes **Offline.** In the Jenkins dashboard, you will see a red "X" next to the node name. This usually happens if the Agent runs out of memory or the Java version is wrong.

---

## How to Set Up an Agent (The Logical Steps)

A senior DevOps engineer follows this checklist when adding a new worker to the cluster:

1.  **Prepare the Machine:** Ensure Java is installed. It *must* match the version on the Controller (e.g., if the Controller uses Java 17, the Agent must use Java 17).
2.  **Define the Node:** In the Jenkins UI, you go to "Manage Nodes" and create a new one. You give it a name and a "Remote Root Directory" (where the work will happen).
3.  **Assign Labels:** You tag the machine (e.g., `production-deployment`). This allows you to say in your Jenkinsfile: `agent { label 'production-deployment' }`.
4.  **Configure the Launch:** Choose "SSH" or "Inbound."
5.  **Test the Connection:** Jenkins will try to connect. If successful, the `remoting.jar` will start, and the machine will show as "In-Sync."

---

## Summary

Master-Agent architecture is all about communication and security.
- **SSH** is the "Chef calls Cook" model, perfect for AWS Linux instances.
- **Inbound** is the "Cook calls Chef" model, perfect for Windows or machines behind firewalls.
- **Security** should always be your top priority. Use SSH keys for connections and **AWS IAM Roles** to handle permissions without sharing secret keys.
- **Java Version** is the most common cause of connection failure. Always keep your cluster on the same version of Java.

In the next file, `03-jenkins-components.md`, we will look at the specific parts of the Jenkins software itself — like the "Workspace," the "Build Queue," and the "Plugin Manager" — to see how they all fit into this architecture.
