# The Options Directive: Setting the Rules of the Game

## 1. Introduction: The Referee and the Sports Match

Imagine you are watching a high-stakes, professional soccer match.
You have the players (the **Agent**).
You have the game plan (the **Stages**).
But a soccer match is more than just people running around a field.
It is a game governed by a strict set of **Rules**.

There is a clock on the field.
If the game takes too long, the referee blows the whistle and ends it.
There is a limit on how many players can be on the field.
There is a "Yellow Card" system for punishing bad behavior.
If there were no rules, the game would descend into chaos.
There would be no way to measure success and no way to protect the
safety of the players.

In the world of Jenkins and DevOps, the **Options Directive** is that
set of rules. It is where you define the "Policies" that govern how
your pipeline behaves.
Whether it is ensuring that your Jenkins server doesn't run out of
disk space (Build Discarder), preventing a job from running forever
(Timeout), or allowing a build to automatically try again if it fails
due to a flaky network (Retry), the options directive is how you
enforce discipline and safety in your automation.

A senior DevOps engineer knows that a pipeline without options is a
"Wild West" script that will eventually crash your server.
In this file, we are going to go into **absolute, extreme technical depth**
on the options available in Declarative pipelines.
We will look at how to protect your AWS storage, how to optimize your
build times, and how to build a "Self-Healing" system that can survive
any disaster.

---

## 2. The `buildDiscarder` Option: Managing Your Digital Graveyard

Every time you run a Jenkins job, it creates data.
It saves log files, it saves test results, and it saves artifacts (like JAR files).
If you don't have a cleanup policy, your Jenkins server will eventually
run out of disk space on your AWS EBS volume.
When the disk hits 100%, Jenkins will crash, and your whole company's
CI/CD system will stop working.

### The Senior Implementation:
```groovy
options {
    // This is the most important policy in any professional job
    buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '5'))
}
```

**What this does:**
It tells Jenkins: "Only keep the last 10 builds in the history.
And only keep the actual artifacts for the last 5 builds."
This ensures that your disk space usage stays "Flat" and predictable.
It turns Jenkins from a "Digital Hoarder" into a "Clean Professional."

---

## 3. The `disableConcurrentBuilds` Option: Preventing Race Conditions

Imagine you have a pipeline that deploys a database schema to AWS.
If two different developers push code at the exact same second,
Jenkins might try to start **Two** deployments at the same time.
These two builds will fight each other over the database, causing
data corruption and "Race Conditions."

### The Senior Implementation:
```groovy
options {
    // Only one version of this job can run at a time
    disableConcurrentBuilds()
}
```

**When to use it:**
-   Database migrations.
-   Deployments to a shared environment (like Staging).
-   Tasks that modify a single AWS resource (like an S3 bucket).
This option is your "Insurance Policy" against parallel chaos.

---

## 4. The `timeout` Option: The Senior Guardrail

We covered this briefly in Module 4, but in the context of the
`options` directive, it is even more powerful.
You can set a global timeout for the **Entire** pipeline.

### The Senior Implementation:
```groovy
options {
    // Kill the entire pipeline if it takes longer than 1 hour
    timeout(time: 1, unit: 'HOURS')
}
```

**Why it matters:**
Sometimes, a Jenkins worker (Agent) might become "Zombie-like."
It might stop responding but not actually die.
Without a global timeout, that build will stay "Running" forever,
wasting your AWS money and blocking other developers.
A 1-hour timeout is the standard for most enterprise applications.

---

## 5. Comparison Table: The Core Options for AWS

| Option | Primary Purpose | Why a Senior Engineer Uses It |
| :--- | :--- | :--- |
| **`buildDiscarder`** | **Disk Safety** | To keep the AWS EBS volume from filling up. |
| **`timeout`** | **Resource Safety** | To kill "Zombie" builds and save money. |
| **`retry`** | **Resiliency** | To handle temporary cloud "Hiccups." |
| **`disableConcurrentBuilds`** | **Data Integrity** | To prevent parallel deployments from clashing. |
| **`timestamps`** | **Visibility** | To see exactly when every event occurred. |

---

## Summary

- The options directive allows you to define global policies that govern pipeline behavior
- buildDiscarder is essential for managing disk space on AWS EBS volumes by rotating old logs
- disableConcurrentBuilds prevents race conditions by ensuring only one instance of a job runs
- A global timeout acts as a "dead man's switch" to terminate zombie builds and save costs
- The retry option improves pipeline resiliency against temporary cloud or network hiccups
- Timestamps should be enabled to provide precise timing data for every line of the build log
- Using options ensures that your automation is disciplined, safe, and production-ready

In the next chapter, `Chapter 12: Declarative Features`, you will learn about conditional logic, manual approvals, and the post section.
