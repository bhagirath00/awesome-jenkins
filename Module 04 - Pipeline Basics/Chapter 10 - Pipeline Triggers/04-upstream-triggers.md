# Upstream Triggers: The Chain Reaction of Automation

## 1. Introduction: The Domino Effect

Imagine you are standing in a massive hall filled with a complex,
intricate arrangement of 10,000 dominoes.
The dominoes are not just in a straight line; they form a beautiful
and complex pattern of loops, spirals, and bridges.
To see the pattern come to life, you don't have to push every single
domino yourself.
You only have to push the **First** one.

Once that first domino falls, it hits the second.
The second hits the third.
The energy travels through the entire system, automatically triggering
every single task in the correct order, at the correct time.
This is a "Chain Reaction."
If one domino fails to fall, the entire pattern stops, preventing the
rest of the system from continuing with incorrect or incomplete work.

In the world of professional DevOps, your software ecosystem is
exactly like those dominoes.
You don't just have one single application.
Instead, you have a "Chain" of dependencies.
Maybe you have a **Core-Library** that is used by a **Payment-Service**,
which is then used by a **Web-Frontend**.
If you update the Core-Library, you need to automatically rebuild and
retest the Payment-Service, and then finally the Web-Frontend.
**Upstream Triggers** are the mechanism that creates this chain reaction.
They allow you to connect separate pipelines together into a logical,
automated "Waterfall" of work.

In this file, we are going to go into **extreme technical depth** on the
strategy and the syntax of connecting pipelines.
We will look at how to build "Microservice Dependency Chains," how to
handle failures in the middle of a reaction, and how to design
a global "Spiderweb" of automation that ensures your entire AWS
infrastructure is always synchronized and up-to-date.

---

## 2. What is an "Upstream" Trigger?

To understand the terminology, you have to think like a hydrologist
(a scientist who studies water).
- **Upstream:** This is where the water comes from (the mountain).
- **Downstream:** This is where the water flows to (the ocean).

In Jenkins, if **Job A** triggers **Job B**:
-   **Job A** is the **Upstream** job.
-   **Job B** is the **Downstream** job.
-   From the perspective of Job B, it has an "Upstream Trigger."
    It is "Watching" Job A and waiting for it to finish.

---

## 3. The Syntax of the Chain: `upstream()`

In a Declarative Pipeline, you use the `upstream` keyword inside the
`triggers { }` block to define this relationship.

### The Basic Syntax:
```groovy
pipeline {
    agent any
    triggers {
        // Trigger this job whenever 'Core-Library-Build' finishes
        upstream(upstreamProjects: 'Core-Library-Build', threshold: hudson.model.Result.SUCCESS)
    }
    stages {
        // ...
    }
}
```

### The Senior Breakdown:
1.  **upstreamProjects:**
    This is a comma-separated list of the jobs you want to watch.
    - **Rule:** You can watch multiple jobs. If *any* of them finish,
      this job will start.
    - **Professional Use Case:** A "System-Integration-Test" job might
      watch 5 different microservice builds.

2.  **The Threshold:**
    This is the most important setting. It tells Jenkins: "What kind
    of result do I require from the upstream job before I start?"
    - **SUCCESS:** Only start if the upstream job is perfect (Blue).
    - **UNSTABLE:** Start even if some tests failed in the upstream job.
    - **FAILURE:** Start only if the upstream job failed (used for
      auto-recovery or cleanup scripts).

---

## 4. The Waterfall vs. The Spiderweb

As a senior architect, you must choose the right "Shape" for your
chain reaction.

### The Waterfall (Sequential)
`Core-Library` -> `API-Service` -> `Front-End` -> `Deployment`
-   **Pros:** Very easy to understand and debug.
-   **Cons:** If one middle step takes a long time, the whole chain
    is delayed. It is a "Single Point of Failure."

### The Spiderweb (Complex Dependency)
Imagine you have a `Database-Schema` job.
When it changes, it triggers 10 different `Microservice` jobs at
the exact same time.
-   **Pros:** Extremely efficient and fast.
-   **Cons:** Harder to visualize. You need a senior-level understanding
    of your AWS network and resource limits to ensure 10 builds
    starting at once don't crash your server.

---

## 5. Parameterized Upstream: Passing the Torch

Sometimes, the upstream job needs to pass "Information" to the
downstream job.
For example, the **Build** job might create a specific version number
(like `v1.2.3`).
It needs to tell the **Deploy** job: "I just finished version `v1.2.3`,
please deploy exactly that version."

### The Senior Solution:
We use a specific step called `build` to trigger the downstream job
manually and pass parameters.
```groovy
stage('Trigger Deploy') {
    steps {
        build job: 'Deploy-to-AWS', 
              parameters: [string(name: 'VERSION', value: '1.2.3')]
    }
}
```
This is a more "Active" way of creating a chain reaction.
Instead of the downstream job "Watching," the upstream job "Pushes"
the work forward with specific instructions.

---

## Summary

- Upstream triggers create a "domino effect" where one pipeline automatically starts another
- The upstream keyword in the triggers block establishes a dependency between different jobs
- Thresholds like SUCCESS or UNSTABLE define exactly when the downstream job should trigger
- Waterfall architectures are simple and sequential, while Spiderweb patterns handle complex dependencies
- The 'build' step allows an upstream job to actively "push" parameters to a downstream job
- Dependency chains are essential for managing modern microservice architectures on AWS
- Upstream triggers provide a clear "chain of custody" for code moving toward production

In the next Module, `Module 5: Declarative Pipelines`, you will start exploring the engine itself, learning the full syntax of Declarative Pipelines for professional use.
