# Dynamic Stages: The Shape-Shifting Pipeline

## 1. Introduction: The Infinite Hotel and the Guest List

Imagine you are the manager of a very special hotel.
This is not a normal hotel with a fixed number of rooms.
Instead, this is an **Infinite Hotel**.
If a single traveler arrives, the hotel creates one room for them.
If a bus with 50 people arrives, the hotel instantly creates 50 rooms.
If a cruise ship with 5,000 people arrives, the hotel instantly
expands to accommodate every single one of them.
The hotel doesn't have a "Static" size.
Instead, its shape and its capacity change in real-time based on the
**Guest List**.

In the world of Jenkins and DevOps, **Dynamic Stages** are that infinite
hotel.
In a Declarative pipeline, the roadmap is "Static."
You must define every stage before the build starts.
But in a Scripted pipeline, the roadmap is **Dynamic**.
Your pipeline can "Read" a list of microservices from a database,
a list of AWS regions from an API, or a list of files from a Git
repository.
It can then use a simple loop to "Generate" as many stages as it needs
to get the work done.
Whether you have 1 app or 1,000 apps, the pipeline adapts perfectly.

In this file, we are going to go into **absolute, exhaustive technical depth**
on how to build "Shape-Shifting" pipelines.
We will look at how to use Groovy loops (for/each) to create stages,
how to handle massive parallelism at scale, and the senior-level
strategies for building "Universal" automation that can manage an
entire global enterprise with just a few lines of code.

---

## 2. Why Go Dynamic? (The Scaling Problem)

As a senior architect, you will eventually face the **Scaling Problem**.

### The Manual Way (Junior):
You have 10 microservices.
You write 10 different Jenkins jobs.
Then, the company grows and you have 100 microservices.
Now you have 100 jobs to maintain.
If you need to change a security setting, you have to do it 100 times.
This is a recipe for burnout and disaster.

### The Dynamic Way (Senior):
You write **One** Jenkins job.
The job starts by asking: "How many microservices are in this repository?"
If it finds 10, it builds 10 stages.
If it finds 100, it builds 100 stages.
Maintenance is perfect. Reliability is perfect.
One change to the code fixes everything for everyone.

---

## 3. The Syntax of the Loop: Generating Stages

In Scripted Pipelines, we use the Groovy `.each` method to iterate
through a list and generate work.

### The Senior Implementation:
```groovy
def regions = ['us-east-1', 'eu-west-1', 'ap-south-1', 'sa-east-1']

node('aws-manager') {
    // We are generating 4 stages dynamically!
    regions.each { regionName ->
        stage("Deploy: ${regionName}") {
            echo "Initiating deployment to the ${regionName} region on AWS..."
            sh "./deploy.sh --region ${regionName}"
        }
    }
}
```

**The Senior Magic:**
If your boss comes to you tomorrow and says "We are launching in
London (eu-west-2)," you don't have to change your code.
You just add `'eu-west-2'` to the list at the top, and the pipeline
automatically grows a new stage.

---

## 4. Advanced: Dynamic Parallelism

Generating 100 stages one after the other is great, but it is slow.
A senior engineer combines dynamic stages with **Parallelism**.

### The Senior Implementation:
```groovy
def microservices = ['auth', 'payment', 'catalog', 'order', 'shipping']
def parallelTasks = [:] // A Map to hold our tasks

microservices.each { svc ->
    // We create a "Task" for every service
    parallelTasks[svc] = {
        node('docker-builder') {
            stage("Build: ${svc}") {
                sh "cd ${svc} && docker build -t ${svc}:latest ."
            }
        }
    }
}

// Now we run ALL of them at the same time!
parallel(parallelTasks)
```

This is the ultimate expression of DevOps power.
You are launching 5 different workers on 5 different AWS nodes,
running 5 different builds, all coordinated by one single loop.

---

## 5. Summary Table: Static vs. Dynamic

| Feature | Static (Declarative) | Dynamic (Scripted) |
| :--- | :--- | :--- |
| **Scalability** | Hard (Manual updates). | **Perfect (Automatic).** |
| **Code Length** | Grows with complexity. | **Stays short.** |
| **Maintenance** | High effort. | **Low effort.** |
| **Visuals** | Predefined roadmap. | **Adapts to the workload.** |
| **Senior Rating**| For single apps. | **For platforms and clusters.** |

---

## Summary

- Dynamic stages allow a pipeline to adapt its structure in real-time based on the workload
- Groovy loops like .each are used to generate stages from lists, file discovery, or API calls
- This approach solves the "scaling problem" by allowing one job to manage hundreds of services
- Combining dynamic stages with the parallel keyword enables massive, concurrent build execution
- Dynamic pipelines remain short and maintainable even as the underlying project grows in complexity
- Using a Map to collect parallel tasks is the standard senior pattern for multi-machine coordination
- Dynamic automation is the foundation for building enterprise-grade DevOps platforms on AWS

In the next file, `03-parallel-execution.md`, you will take a deep dive into the complex world of concurrency and resource management at scale.
