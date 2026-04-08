# Stage Options: Fine-Tuning Your Roadmap

## 1. Introduction: The Pilot's Checklist

Imagine you are the pilot of a high-performance jet aircraft.
You have a flight plan that tells you exactly where you are going.
But a flight plan is just a list of locations.
To actually fly the plane safely, you need to set specific **Conditions**
and **Limits** for every part of the journey.

When you are taking off, you might set a "Timeout."
If the plane doesn't reach a certain speed within a certain number of
seconds, you abort the takeoff.
When you are flying through a storm, you might set a "Retry" limit.
If the engine flickers once, you keep going.
If it flickers three times, you change your route.
These conditions and limits are the **Options** of your flight.
They don't change *where* you are going, but they change *how* you
behave during the journey.

In the world of Jenkins Pipelines, **Stage Options** are those settings.
They allow you to add "Intelligence" and "Safety" to your stages.
Whether it is ensuring a stage doesn't run forever (Timeout),
re-trying a flaky network command (Retry), or ensuring that only one
build can access a specific AWS resource at a time (Lock),
Stage Options are what separate a "Brittle" script from a "Resilient"
production-grade automation system.

In this file, we are going to go into **absolute, extreme technical depth**
on the options available inside a Declarative stage.
We will look at how to manage time, how to handle failures gracefully,
and how to protect your company's AWS infrastructure from being overwhelmed
by too many parallel tasks.

---

## 2. Timeout: The "Dead Man's Switch" of DevOps

The most important option for any senior DevOps engineer is the **Timeout**.
Imagine you have a stage that runs a database migration on AWS.
Usually, this takes 5 minutes.
But today, there is a "Lock" on the database, and the migration script
sits there forever, waiting for the lock to be released.

Without a timeout, your Jenkins job will stay "Running" forever.
It will consume an AWS executor, it will block other builds,
and it will cost your company money for absolutely no work.

### The Senior Implementation:
```groovy
stage('Database Migration') {
    options {
        // Kill this stage if it takes longer than 10 minutes
        timeout(time: 10, unit: 'MINUTES')
    }
    steps {
        sh './run-migrations.sh'
    }
}
```

**Rule of Thumb:** Every stage that talks to an external system (AWS,
Databases, APIs) MUST have a timeout.
It is better to have a build fail after 10 minutes than to have it
hang for 10 hours.

---

## 3. Retry: Handling the "Flaky" Cloud

The cloud is a wonderful thing, but it is not perfect.
Sometimes, a network connection to an AWS S3 bucket fails for a
split second.
Sometimes, a Docker registry is temporarily overloaded.
If your pipeline fails the first time it sees a tiny "Hiccup," you
will be wasting your time debugging "Ghost" errors that aren't real.

### The Senior Implementation:
```groovy
stage('Pull Docker Image') {
    options {
        // If this stage fails, try again up to 3 times before giving up
        retry(3)
    }
    steps {
        sh 'docker pull my-company-repo/my-app:latest'
    }
}
```

**The Warning:** Only use `retry` for stages that are **Idempotent**.
This means it is safe to run them twice.
Don't use `retry` for a stage that "Charges a Credit Card" or
"Deletes a Database," or you might end up with a disaster!

---

## 4. Timestamps: The Clock of Truth

When you are looking at a 1,000-line log file from a failed build,
the most common question is: "When did this happen?"
By default, Jenkins logs don't show the time.

### The Senior Implementation:
```groovy
stage('Heavy Compilation') {
    options {
        timestamps()
    }
    steps {
        sh 'mvn clean install'
    }
}
```

With this option enabled, every single line of the log will have a
timestamp (e.g., `14:02:33`).
This allows you to see exactly which unit test is taking 5 minutes
and which one is taking 5 seconds.
It is the ultimate tool for performance tuning your AWS builds.

---

## 5. Comparison: Which Option Should You Use?

| Option | Primary Purpose | Senior Use Case |
| :--- | :--- | :--- |
| **Timeout** | **Safety** | Preventing "Zombie" builds that hang forever. |
| **Retry** | **Resiliency** | Handling temporary AWS network "Hiccups." |
| **Timestamps** | **Visibility** | Finding performance bottlenecks in logs. |
| **SkipDefaultCheckout** | **Performance** | Speeding up jobs that don't need code. |
| **BuildDiscarder** | **Cleanup** | Keeping your AWS EBS volume from filling up. |

---

## Summary

- Stage options allow you to add "intelligence" and safety guardrails to your pipeline stages
- The timeout option is critical for preventing "zombie" builds from hanging indefinitely
- Retry logic helps handle temporary network hiccups or flaky cloud infrastructure
- Timestamps provide the exact timing data needed for performance tuning and log analysis
- Only use the retry option for idempotent tasks that are safe to execute multiple times
- BuildDiscarder and SkipDefaultCheckout help optimize storage and performance on AWS
- Combining these options creates a resilient, production-grade automation system

In the next file, `04-build-deploy-test-stages.md`, you will see how to combine stages, triggers, and options into a complete deployment roadmap.
