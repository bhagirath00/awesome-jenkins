# Parallel Stages: Speeding Up the Pipeline

## 1. Introduction: The Supermarket Checkout Analogy

Imagine you are at a massive grocery store on a busy Saturday afternoon.
Every single person in the store has a full cart of groceries.
Now, imagine if the store only had **one single checkout line**.
Even if the cashier was the fastest in the world, the line would
eventually stretch out the door and around the block.
A customer who only has a carton of milk would have to wait 2 hours
behind the person buying groceries for a whole month.
This is a "Sequential" system, and it is the enemy of efficiency.

To solve this, the supermarket uses **Parallelism**.
They open 20 different checkout lines at the same time.
Instead of one line moving slowly, 20 lines are moving simultaneously.
A task that would have taken 20 hours to complete now only takes 1 hour.
The "Throughput" of the store has increased by 2,000%.

In the world of DevOps, your pipeline is that supermarket.
By default, Jenkins runs every stage one after the other (Sequentially).
But many tasks in a build—like running unit tests, static code analysis,
and security scans—don't actually depend on each other.
They can all happen at the exact same time.
By using **Parallel Stages**, you can cut your total build time from
30 minutes down to 5 minutes.
In a professional AWS environment, where every minute of developer time
costs money, mastering parallelism is the mark of a senior engineer.

In this file, we are going to go into **extreme technical depth** on how
to implement parallel stages in your Jenkinsfile.
We will look at the syntax, the architectural impact on your AWS agents,
and how to handle the complex "Fan-In" and "Fan-Out" logic of a
high-performance CI/CD system.

---

## 2. The Syntax of Parallelism

In a Declarative Pipeline, you use the `parallel` block to define
stages that should run at the same time.

### The Basic Structure:
```groovy
stage('Quality Checks') {
    parallel {
        stage('Unit Tests') {
            steps {
                sh 'npm run test:unit'
            }
        }
        stage('Static Analysis') {
            steps {
                sh 'npm run lint'
            }
        }
        stage('Security Scan') {
            steps {
                sh 'npm run security-check'
            }
        }
    }
}
```

### The Senior Breakdown:
1.  **The Parent Stage:**
    The `parallel` block must live inside a "Parent" stage (in this case,
    'Quality Checks').
    This parent stage acts as a container.
    The pipeline will only move past 'Quality Checks' once **all** the
    stages inside it have finished successfully.

2.  **Implicit Concurrency:**
    Jenkins will automatically try to find a worker machine (Node) for
    each of these stages.
    If you have 3 stages in parallel and 3 available workers, they will
    all start at the exact same second.

---

## 3. The Architecture: Managing Your AWS Workers

Parallelism is powerful, but it requires resources.
If you tell Jenkins to run 10 stages in parallel, but you only have
one AWS EC2 agent, Jenkins will be forced to run them one by one anyway.
This is called "Resource Starvation."

### The "Agent" Strategy:
A senior engineer often defines specific agents for parallel stages.
- **Example:** You might run the "Compiling" stage on a massive `m5.xlarge` instance.
- But you can run the 10 "Unit Test" stages on 10 tiny `t3.micro` instances.
- This "Fan-Out" strategy allows you to use the right tool for the job
  and saves your company a massive amount of money on AWS.

---

## 4. Visualizing Parallelism in Blue Ocean

Standard Jenkins UI is not very good at showing parallel stages.
They often just look like a single long box.
To truly see your parallelism in action, you must use **Blue Ocean**.

In Blue Ocean, parallel stages are shown as a "Vertical Stack."
You can see all three checkout lines moving at the same time.
If one line gets stuck (fails), the other lines keep moving.
This visualization is essential for debugging.
You can immediately see: "Aha! The Unit Tests passed, but the Security
Scan failed. I need to fix the security issue, but the code itself
is functionally correct."

---

## 5. Advanced Parallelism: Fail-Fast Logic

What happens if you have 10 parallel stages, and the first one fails
within 10 seconds?
Do you want to wait 20 more minutes for the other 9 stages to finish?
Usually, the answer is **NO**. If one part of the quality gate fails,
the whole build is a failure.

### The `failFast` Directive:
```groovy
stage('Parallel Tests') {
    failFast true
    parallel {
        // ... your stages here ...
    }
}
```

- **How it works:** If any stage inside the parallel block fails,
  Jenkins will immediately send a "Stop" signal to all other stages.
- **The Benefit:** It frees up your AWS agents immediately so they
  can start working on the next build.
- **The Result:** Faster feedback for the developers and lower costs
  for the company.

---

## Summary

- Parallelism allows multiple independent stages to run at the same time, significantly reducing build duration
- The parallel block must be contained within a parent stage that coordinates the child tasks
- Jenkins automatically assigns available workers (Agents) to each parallel branch
- The "Fan-Out" strategy allows you to use different-sized AWS agents for different types of work
- Blue Ocean is the recommended UI for visualizing and debugging complex parallel workflows
- The failFast directive stops all parallel branches immediately if any one of them fails
- Proper use of parallelism is essential for scaling CI/CD in a microservices environment

In the next file, `03-stage-options.md`, you will learn how to add advanced controls like timeouts and retries to your pipeline stages.
