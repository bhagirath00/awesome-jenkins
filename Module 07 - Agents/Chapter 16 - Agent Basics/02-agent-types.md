# Agent Types: Specialized Workers for Every Task

## 1. Introduction: The Specialized Crew

Imagine you are still the architect from the previous file, but now
your project has become much more complex.
You are no longer just building a simple office tower.
Now, you are building a **Complex Smart City**.
You need to install massive electrical systems, you need to build
underground tunnels, and you need to landscape high-tech gardens.

You can't just hire a "General Worker" for every task.
A general worker doesn't know how to wire a high-voltage transformer.
A general worker doesn't know how to operate a specialized tunnel-boring
machine.
Instead, you need a **Specialized Crew**.
You need electricians for the power, engineers for the tunnels, and
botanists for the gardens.
Each worker has a specific "Skill Set" and "Tool Set" that makes them
perfect for their specific job.

In the world of Jenkins, **Agent Types** are those specialized workers.
Different tasks require different environments.
Some jobs need Linux, some need Windows.
Some jobs need a massive amount of RAM for a Java build, and some
need a high-speed GPU for AI model testing.
A senior DevOps engineer knows how to build a diverse workforce of
agents so that every job has the "Right Tool for the Task."

## 2. Static vs. Dynamic Agents

This is the most important distinction in agent management.

### Static Agents (The Permanent Employees)
These are servers that are "Always On." They sit in your AWS account
waiting for work 24/7.
-   **Pros:** Very fast start time; the files are already there.
-   **Cons:** Expensive; you pay for them even when they are idle.
-   **Best For:** Critical, high-frequency jobs.

### Dynamic Agents (The Contract Workers)
These agents are created "On Demand." Jenkins talks to the AWS API or
Docker and launches a new worker only when a job starts.
Once the job is finished, the worker is "Terminated" and disappears.
-   **Pros:** Extremely cost-efficient; you only pay for what you use.
-   **Cons:** Slower start time (wait for the machine to boot).
-   **Best For:** 90% of your CI/CD workload.

---

## 3. The Docker Agent: The Modern Standard

In a modern AWS enterprise, most agents are actually **Docker Containers**.
Instead of launching a whole virtual machine, Jenkins launches a
lightweight container that contains exactly the tools you need (like Node.js,
Python, or Maven).
This is the "Gold Standard" for scalability and hygiene because every
build starts with a perfectly clean environment.

---

## Summary

- Specialized agents ensure that every job has the correct OS and hardware resources
- Static agents are always on and provide fast start times for critical builds
- Dynamic agents are created on-demand, maximizing AWS cost efficiency
- Docker agents provide lightweight, ephemeral environments for modern CI/CD
- Choosing the right agent type is a balance between build speed and infrastructure cost
- Offloading heavy tasks to specialized workers keeps the primary Jenkins controller healthy
- Cloud-native agent management uses auto-scaling to match the workforce to the workload

In the next file, `03-configuring-agents.md`, you will learn the technical steps to connect and configure these agents in the Jenkins UI.
