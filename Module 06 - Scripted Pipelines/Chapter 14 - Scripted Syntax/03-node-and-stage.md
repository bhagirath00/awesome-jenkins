# Node and Stage: The Machinery of Scripted Pipelines

## 1. Introduction: The Landlord and the Apartment

Imagine you are a traveler who is arriving in a new city for a
3-month project.
You don't want to buy a house in this city.
That would be too expensive, too much responsibility, and you don't
need it once the project is over.
Instead, you want to **Lease** an apartment.
You talk to a **Landlord**.
You tell the landlord: "I need an apartment with two bedrooms and
a high-speed internet connection."
The landlord looks at their list of properties, finds one that matches
your requirements, and gives you the key.

For the next 3 months, that apartment is yours.
You can move your furniture in, you can cook your meals, and you
can do your work.
But you must follow the landlord's rules.
And most importantly, when your project is finished, you must
hand the key back to the landlord so that someone else can use
the apartment.

In the world of Jenkins and AWS, the **Node Block** is that landlord.
The "Apartment" is an **AWS EC2 instance**.
When you write `node('linux') { ... }`, you are telling Jenkins:
"Find me a machine with the 'linux' label and let me lease it
for a while."
A senior DevOps engineer knows that this "Lease" is the most expensive
part of the automation process.
If you lease a machine but don't use it, you are wasting money.
If you forget to "Return the Key," you are causing a resource leak.
In this file, we are going to go into **absolute, exhaustive technical depth**
on the mechanics of nodes and stages in Scripted pipelines.
We will look at how to manage your AWS workforce, how to visualize
your work with stages, and the senior-level patterns for ensuring
that your leases are always returned and your servers stay clean.

---

## 2. The Anatomy of a Node Lease

In a Scripted Pipeline, the `node` block is a function that takes
two arguments: a **Label** and a **Closure** (the code block).

### The "Label" Strategy:
A senior engineer uses labels to build a "Smart" workforce.
- `node('docker')`: "Give me any machine that has Docker installed."
- `node('windows && ssd')`: "Give me a Windows machine that has
  fast SSD storage."
- `node('master')`: "Execute this code on the main Jenkins server
  itself." (Warning: Only do this for very small, non-intensive tasks!)

### The "Workspace" Lifecycle:
The second you enter a `node` block, Jenkins creates a folder on
the AWS server called the **Workspace**.
Everything you do—every `git clone`, every `npm install`—happens
inside this folder.
- **The Senior Rule:** A node lease and a workspace go together.
- You cannot access files from a "Linux" workspace when you are
  running code on a "Windows" node.
- If you need to move data between them, you must use **Stashing** or
  **Amazon S3**.

---

## 3. The Stage: The Roadmap for Humans

As we learned in Chapter 14, the `stage` keyword is optional in
Scripted pipelines.
But a professional pipeline **Always** uses stages.

### Why?
Because Jenkins uses stages to create the "Stage View" dashboard.
If you have a script that takes 60 minutes to run, and it fails at
minute 55, you don't want to search through 100,000 lines of logs.
You want to look at the dashboard and see that the **"Post-Deployment Test"**
stage is red.

### The Syntax:
```groovy
node('aws-agent') {
    stage('Preparation') {
        echo 'Cleaning the workspace...'
        cleanWs()
    }
    stage('Execution') {
        sh './run-heavy-task.sh'
    }
}
```

---

## 4. Resource Leaks: The Silent Killer

One of the biggest dangers of Scripted pipelines is that they are
**Too Powerful**.
In a Declarative pipeline, Jenkins handles the "End of the Lease"
automatically.
In a Scripted pipeline, if your script has a bug and hangs forever,
the node lease stays open.

### The Result:
The AWS EC2 instance stays "Busy."
No other developers can use it.
The "Queue" in Jenkins gets longer and longer.
And your AWS bill gets bigger and bigger.

### The Senior Solution:
Always wrap your `node` blocks in a **`timeout`**.
```groovy
timeout(time: 2, unit: 'HOURS') {
    node('big-server') {
        // ... your work here ...
    }
}
```
This ensures that even if your script fails or hangs, the lease
will be "Forced" to end after 2 hours, saving your resources
and your money.

---

## Summary

- The node block is a function that "leases" a specific worker machine from the Jenkins cluster
- Labels are used within the node block to request machines with specific OS or hardware capabilities
- Every node block creates a localized workspace; data cannot be shared directly between different nodes
- Stages are optional in Scripted pipelines but are essential for providing visibility on the dashboard
- A senior best practice is to wrap node blocks in timeouts to prevent indefinite resource leaks on AWS
- Scripted pipelines allow for multiple and even nested node blocks for complex multi-machine workflows
- Resource management is a key responsibility of a senior engineer when using Scripted syntax

In the next file, `04-error-handling.md`, you will learn how to implement robust error handling using try-catch-finally blocks.
