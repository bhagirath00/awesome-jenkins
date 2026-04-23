# Configuring Agents: The Connection Protocol

## 1. Introduction: The Radio Link

Imagine you are the flight director for a **Deep Space Probe**.
You are sitting in the mission control center on Earth.
The probe is millions of miles away, hurtling through the solar system.
The probe is a highly advanced robot (the **Agent**).
It has sensors, it has engines, and it can perform complex tasks.
But for the probe to do its job, it must be "Configured" and
"Connected" to the mission control (the **Controller**).

You need to establish a **Radio Link**.
You need to tell the probe exactly how to talk back to Earth.
You need to provide it with "Security Keys" so that no one else can
hijack the connection.
And you need to define its "Safe Operating Limits" so that it doesn't
burn out its batteries or crash into a planet.

In the world of Jenkins, **Configuring an Agent** is establishing that
radio link.
It is the process of defining the agent's identity, its security
credentials, and its technical capabilities in the Jenkins UI.
Without a proper configuration, your worker machine is just an
expensive piece of AWS hardware sitting idle in the cloud.

## 2. Defining the "Node" in Jenkins

In the Jenkins dashboard, an agent is managed as a "Node."
When you create a new node, you are filling out a "Profile" for your
worker.

### Key Configuration Fields:
1.  **Name:** A unique identifier (e.g., `linux-build-01`).
2.  **Remote Root Directory:** The "Home Folder" where Jenkins will
    store the workspace and build tools on the agent.
3.  **Labels:** The "Skills" of the agent (e.g., `docker`, `maven`, `aws`).
4.  **Usage:** Should this agent run any job, or only jobs that
    specifically ask for its labels? (The senior choice is usually
    "Only jobs with matching labels").

---

## 3. The Security Handshake

Connecting an agent requires a **Security Handshake** to ensure that
your Jenkins environment is protected.

-   **SSH Keys:** For Linux agents, you provide the controller with
    a private key that allows it to log into the worker machine
    automatically.
-   **Secret Tokens:** For Inbound (JNLP) agents, Jenkins generates a
    unique, long-string secret. You must provide this secret to the
    agent software so it can prove its identity to the controller.

---

## Summary

- Configuring an agent involves defining its identity and capabilities in the Jenkins UI
- The Remote Root Directory is the primary workspace for all build tasks on the agent
- Labels are used to categorize agents by their specific tools or hardware features
- Setting usage to "Only build jobs with matching labels" is a senior-level best practice
- The security handshake ensures that only authorized machines can join the worker pool
- SSH is the preferred connection protocol for secure, Linux-based AWS agents
- Proper configuration prevents job failures and ensures efficient resource allocation

In the next file, `04-agent-labels.md`, you will learn how to use labels to create an intelligent and self-routing workload.
