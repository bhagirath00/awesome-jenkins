# What is an Agent? The Workforce of Jenkins

## 1. Introduction: The Architect and the Construction Crew

Imagine you are a world-class **Architect**.
You have a brilliant mind, and you have spent months designing a
magnificent skyscraper.
You have the blueprints, you have the calculations, and you know exactly
how every piece of steel and glass should fit together.
But as an architect, you don't actually go to the construction site
and carry the heavy beams yourself.
You don't operate the cranes, and you don't pour the concrete.
You are the "Brain" of the operation, but you are not the "Muscle."

To turn your blueprint into a building, you need a **Construction Crew**.
The crew consists of workers, machines, and tools.
They follow your orders perfectly, doing the heavy lifting and the
manual labor while you sit in your office and coordinate the project.

In the world of Jenkins, the **Jenkins Controller** is the architect.
It holds the configurations, the users, and the job definitions.
The **Jenkins Agents** are the construction crew.
They are the actual machines (often AWS EC2 instances) that perform the
heavy lifting of your automation—running the builds, executing the
tests, and deploying the code.

## 2. The Distributed Architecture

Jenkins is designed to be a **Distributed System**.
A senior DevOps engineer never runs heavy jobs on the "Controller" (the
main Jenkins server).
If you do, the controller will run out of memory, the UI will freeze,
and your entire automation platform will crash.

### The Standard Pattern:
1.  **The Controller:** Manages the UI, the security, and the job queue.
2.  **The Agents:** Receive instructions from the controller and do the
    actual work on their own CPU and disk.

This separation of "Management" and "Work" allows you to scale your
Jenkins cluster to thousands of builds per day by simply adding more
agents to the workforce.

---

## 3. How Agents Connect

For a construction crew to work, they need to be able to hear the
architect's instructions. In Jenkins, there are two main ways an agent
connects to the controller:

-   **Inbound Connection (JNLP):** The agent "calls" the controller to
    ask for work. This is great for workers behind a firewall.
-   **Outbound Connection (SSH):** The controller "reaches out" and logs
    into the agent using a secure key. This is the professional standard
    for Linux-based agents on AWS.

---

## Summary

- The Jenkins Controller acts as the "brain," managing configurations and the job queue
- Jenkins Agents are the "muscle" that perform the actual build and test execution
- Distributed architecture prevents the controller from crashing due to resource exhaustion
- Agents can connect via SSH for Linux environments or JNLP for workers behind firewalls
- Scaling a Jenkins cluster is achieved by adding more agents to the worker pool
- Offloading work to agents ensures the Jenkins UI remains responsive for the entire team
- Mastering agent management is the foundation for building a high-performance CI/CD platform

In the next file, `02-agent-types.md`, you will explore the different types of agents and how to choose the right one for your workload.
