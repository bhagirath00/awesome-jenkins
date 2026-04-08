# Pipeline Triggers: Automating the Automation

## 1. Introduction: The Alarm Clock and the Event

Imagine you are a world-class security guard at a high-security facility.
Your job is to monitor the front gate and the security cameras.
However, you aren't just sitting there looking at the screen 24/7.
That would be incredibly boring and inefficient.
Instead, you rely on **Sensors**.

You have a sensor on the front gate.
When a car arrives, a buzzer sounds in your office.
You have a sensor on the perimeter fence.
When someone tries to climb over, an alarm goes off.
You also have an alarm clock that goes off at 6:00 PM to tell you
it's time to do your hourly patrol.
The buzzer, the alarm, and the clock are all **Triggers**.
They are events that tell you: "Stop what you are doing and start
this specific task now."

In the world of Jenkins Pipelines, **Triggers** are those sensors.
A pipeline without a trigger is just a piece of code sitting in a
Git repository.
It has the potential to do work, but it never starts.
A trigger is the "Spark" that brings the pipeline to life.
Whether it's a developer pushing code to GitHub, a specific time of day,
or another pipeline finishing its work, triggers are what make your
CI/CD system truly "Automated."

In this file, we are going to go into **extreme technical depth** on the
philosophy of triggers.
We will look at the different types of sensors you can build, how they
interact with your AWS infrastructure, and why choosing the right trigger
is the difference between a real-time DevOps engine and a slow, manual
bottleneck.

---

## 2. The Philosophy of Triggers: Push vs. Pull

As a senior engineer, you must understand the two fundamental ways
to trigger a pipeline.
This is not just a technical choice; it is an architectural decision
that affects your server load and your team's velocity.

### A. The "Pull" Model (SCM Polling)
In the pull model, Jenkins is the one who takes action.
Every few minutes (e.g., every 5 minutes), Jenkins wakes up and
calls the GitHub API.
It asks: "Hey, is there any new code on the 'main' branch?"
If GitHub says "Yes," Jenkins pulls the code and starts the build.

**The Senior Critique:**
-   **Pros:** It works even if your Jenkins server is hidden behind
    a very strict corporate firewall that doesn't allow any incoming traffic.
-   **Cons:** It is incredibly inefficient. Jenkins wastes CPU and
    network bandwidth asking the same question over and over again.
-   **Latency:** If a developer pushes code 1 minute after the last
    poll, the build won't start for 4 more minutes.

### B. The "Push" Model (Webhooks)
In the push model, GitHub is the one who takes action.
The second a developer runs `git push`, GitHub sends a "POST" message
directly to your Jenkins server.
This message says: "Hey! New code just arrived. Start the build now."

**The Senior Recommendation:**
-   **Pros:** It is **Instant**. There is zero delay.
-   **Efficiency:** Jenkins sits in silence doing nothing until there
    is actual work to do.
-   **Scalability:** This is how you manage 1,000 different pipelines
    without crashing your server.

---

## 3. Configuring Triggers in the Jenkinsfile

In a Declarative Pipeline, you define your triggers inside a special
block called `triggers { }`.
This block lives at the top of your pipeline, right next to the `agent`
and `environment` sections.

### The Basic Syntax:
```groovy
pipeline {
    agent any
    triggers {
        // Trigger 1: Poll SCM every 5 minutes (H means Hash)
        pollSCM('H/5 * * * *')
        
        // Trigger 2: Periodic build every morning at 2 AM
        cron('H 2 * * *')
        
        // Trigger 3: Start when code is pushed to GitHub
        githubPush()
    }
    stages {
        // ...
    }
}
```

---

## 4. The "H" (Hash) Symbol: The Load Balancer

If you have 100 different pipelines, and you tell all of them to
run at exactly 2:00 AM (`0 2 * * *`), your server will crash.
The CPU will spike to 100%, and the builds will fail.

A senior engineer always uses the **`H`** symbol.
`H 2 * * *` tells Jenkins: "Run this job sometime between 2:00 AM
and 2:59 AM."
Jenkins will calculate a unique minute for each job (e.g., Job A
starts at 2:14, Job B starts at 2:38).
This "Spreads the Load" across the hour, keeping your AWS EC2
instance healthy and happy.

---

## 5. Triggers and Parameters: The Dynamic Start

Sometimes, a trigger needs to pass information into the pipeline.
For example, if you use the "Generic Webhook Trigger" plugin,
you can send a JSON message to Jenkins.
The trigger can "Parse" that JSON and extract values like
`VERSION` or `ENVIRONMENT`.

These values then become **Environment Variables** inside your pipeline.
This allows you to build a single deployment pipeline that can
target "Dev," "Staging," or "Production" depending on what the
trigger tells it.
This is the ultimate tool for "ChatOps" where a message in Slack
can trigger a complex, parameterized deployment to AWS.

---

## Summary

- Triggers act as the "sensors" that automatically start your pipeline in response to events
- The Push model (Webhooks) is the instant, efficient standard for modern DevOps teams
- The Pull model (Polling) is a resource-heavy fallback for restricted network environments
- Cron triggers are essential for scheduling recurring tasks like nightly builds or backups
- The 'H' (Hash) symbol is a critical tool for load balancing scheduled tasks across a cluster
- Triggers can be combined in the 'triggers' block to support multiple automation scenarios
- Dynamic triggers can parse external metadata to pass variables directly into the build

In the next file, `02-github-webhook-trigger.md`, you will learn how to configure the industry-standard GitHub Webhook for real-time automation.
