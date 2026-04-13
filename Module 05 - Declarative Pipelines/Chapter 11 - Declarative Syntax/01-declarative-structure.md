# Declarative Pipeline Structure: The Grammar of Automation

## 1. Introduction: The Legal Contract and the Code

Imagine you are a senior executive at a massive global corporation.
You are about to sign a multi-million dollar contract with a new partner.
This contract is not just a collection of random ideas or a friendly handshake.
It is a **Structured Legal Document**.
It has a very specific "Grammar" that must be followed perfectly.

The contract starts with the **Parties** involved (who is doing the work).
Then it lists the **Effective Date** (when the work starts).
It defines the **Scope of Work** (what exactly will be done).
Finally, it lists the **Terms and Conditions** (what happens if things go wrong).
If you put the scope of work at the top and the parties at the bottom,
the lawyers will laugh at you and the contract will be invalid.

In the world of Jenkins, the **Declarative Pipeline** is that legal contract.
It is the "Formal Dialect" of automation.
It was designed to bring order, structure, and readability to the
complex world of CI/CD.
By following a strict, predefined set of rules (the "Syntax"),
you ensure that your automation is not just a "Script" that only you
understand, but a professional "System" that your entire team can read,
maintain, and trust.

In this file, we are going to go into **absolute, extreme technical depth**
on the structure of a Declarative pipeline.
We will look at the mandatory blocks, the rules of the "Grammar,"
and the senior-level philosophy of designing a pipeline that is
as clean and professional as a legal contract.

---

## 2. The Mandatory Root: The `pipeline` Block

Every Declarative pipeline in existence MUST start and end with the
`pipeline { }` block.
This is the "Outer Hull" of your automation spaceship.

### The Purpose of the Root Block:
When Jenkins opens your `Jenkinsfile`, the first word it looks for is `pipeline`.
If it finds it, Jenkins immediately switches its brain into
"Declarative Mode."
It turns on the "Syntax Checker" and prepares the "Visual Dashboard."
If you try to write Declarative code without this block, Jenkins will
be confused and your build will fail before it even starts.

### The Hierarchy of Power:
Inside the `pipeline` block, everything follows a strict "Nesting" rule.
-   **Directives:** These are the high-level settings (like `agent` or `triggers`).
-   **Sections:** These are the containers for work (like `stages` or `post`).
-   **Steps:** These are the actual commands (like `sh` or `echo`).
A senior engineer understands that this hierarchy is the key to
organizing complex AWS deployments.

---

## 3. The `agent` Directive: Defining the Worker

After the `pipeline` block, the very next thing you must define is the `agent`.
This is where you tell Jenkins **which machine** should do the work.

### Why "Agent" is Mandatory:
Imagine you have a factory with 1,000 workers.
If you give an order but don't specify *which* worker should do it,
nothing will happen.
Everyone will just look at each other and wait.
In Jenkins, the `agent` directive is how you "Assign the Task" to a machine.

### Common Agent Strategies for AWS:
-   `agent any`: Use any available machine in your AWS cluster.
-   `agent none`: Do not assign a machine yet. (Used when different stages
    need different types of machines).
-   `agent { label 'linux-builder' }`: Only use a machine that has the
    specific tag 'linux-builder'.

---

## 4. The `stages` Section: The Sequential Roadmap

The `stages` section is the heart of your pipeline.
It is a container that holds a list of individual `stage` blocks.
In a Declarative pipeline, stages always run **Sequentially** (one after
the other) unless you specifically use the `parallel` keyword.

### The Rule of One:
A Declarative pipeline can only have **One** `stages` section.
This forces you to think about your automation as a single, logical flow.
If you find yourself wanting two different `stages` sections,
it's a sign that you should probably split your work into two
different pipelines.

---

## 5. The `post` Section: The "Exit Interview"

What happens if your pipeline fails in the middle of a deployment?
Or what if it succeeds and you want to tell the team?
The `post` section is where you handle the "Aftermath" of your automation.

### The Power of the `post` Conditions:
-   `always`: Run this code no matter what happened (e.g., cleanup).
-   `success`: Only run if the whole pipeline was perfect.
-   `failure`: Only run if something went wrong (e.g., send an alert).
-   `unstable`: Run if the build finished but the tests failed.

A senior engineer uses the `post` section to ensure that the server
is always left in a clean state and the team is always informed.

---

## Summary

- Declarative Pipelines provide a formal, structured "grammar" for defining automation
- Every Declarative pipeline must be wrapped in a mandatory 'pipeline' block
- The 'agent' directive is used to assign work to specific machines or containers
- The 'stages' section is a unique container that holds the sequential roadmap of tasks
- The 'post' section handles the aftermath of the build, such as cleanup and notifications
- Strict syntax rules allow Jenkins to perform pre-build validation to catch errors early
- Maintaining a clean hierarchy and proper nesting is essential for professional code

In the next file, `02-agent-directive.md`, you will take a deep dive into the world of Jenkins agents, including Docker and AWS-specific configurations.
