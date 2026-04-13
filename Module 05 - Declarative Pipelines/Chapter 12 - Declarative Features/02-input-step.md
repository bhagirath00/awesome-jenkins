# The Input Step: Human-in-the-Loop Automation

## 1. Introduction: The "Big Red Button"

Imagine you are the chief security officer at a high-end nuclear power plant.
The entire facility is automated with the most advanced computers in
the world.
The computers monitor the temperature, the pressure, and the energy output
with perfect precision.
They can adjust the controls in a microsecond.

However, there is one specific task that the computers are **not**
allowed to do on their own.
If the facility needs to perform a "Purge" of the cooling system,
a task that is incredibly dangerous and irreversible, the computer
must stop.
It must sound an alarm and wait for a human being—a qualified engineer
with a physical key—to walk over to the control panel and press
the **Big Red Button**.
The computer can suggest the action, it can prepare the action, but
it cannot *execute* the action until a human says "Yes."

In the world of Jenkins and DevOps, the **Input Step** is that "Big Red
Button." It is the mechanism that allows your fully automated pipeline
to stop and wait for a human to give their approval.
Whether it is a manager approving a production deployment, a QA
engineer confirming that the manual tests passed, or a senior dev
reviewing a complex database migration, the input step is how you
build a "Safe" bridge between the world of robots and the world of
humans.

A senior DevOps engineer knows that "100% Automation" is often a
fantasy. In the real world, there are legal requirements, compliance
rules, and common-sense safety checks that require a human brain.
In this file, we are going to go into **absolute, exhaustive technical depth**
on the input step.
We will look at how to pause your pipeline, how to get data from
a user during the pause, and how to set up "Auto-Aborting" logic so
that your AWS resources aren't wasted if no one shows up to click
 the button.

---

## 2. The Philosophy of Manual Approval

As a senior architect, you must decide where the "Human Gate" should be.
This is a choice between speed and safety.

### The "Continuous Deployment" Ideal:
In a perfect world, every commit to the `main` branch would go
automatically to production without any human intervention.
- **Goal:** Zero delay.
- **Reality:** Very few companies are actually ready for this.

### The "Continuous Delivery" Reality:
In most professional environments, the code is "Ready" to be deployed,
but a human makes the final decision on **when** to do it.
- **Why?** Maybe the marketing team isn't ready for the new feature.
- **Why?** Maybe the database team needs to do a manual backup first.
The `input` step is the tool that makes "Continuous Delivery" possible.

---

## 3. The Basic `input` Syntax: Pausing the Machine

The simplest form of the input step is a single line of code that stops
 the build and waits for a "Proceed" or "Abort" click.

### The Senior Implementation:
```groovy
stage('Approval: Production Gate') {
    steps {
        // This stops the pipeline and shows a message on the dashboard
        input message: 'Do you want to deploy this version to AWS Production?'
    }
}
```

**What happens under the hood:**
When Jenkins hits this line, it immediately "Releases" the worker machine
(the Agent) so that it can do other work.
The pipeline enters a state of "Suspension."
It sits there using zero CPU and zero memory until someone logs into
the Jenkins UI and clicks the button.

---

## 4. Parameterized Input: Getting Data from Humans

Sometimes, you don't just want a "Yes" or "No."
You want the human to give you a specific piece of information.
Maybe you want them to choose which AWS region to deploy to, or
what "Version Name" to give the release.

### The Senior Implementation:
```groovy
stage('Manual Release Config') {
    steps {
        script {
            def userInput = input(
                id: 'release-config',
                message: 'Provide release details',
                parameters: [
                    string(name: 'TAG_NAME', defaultValue: 'v1.0.0', description: 'Version Tag'),
                    choice(name: 'ENVIRONMENT', choices: ['Staging', 'Production'], description: 'Target Env')
                ]
            )
            // Now you can use the human's input in your next steps
            echo "A human told me to deploy ${userInput.TAG_NAME} to ${userInput.ENVIRONMENT}"
        }
    }
}
```

This "Self-Service" model is incredibly powerful.
It allows a senior DevOps engineer to build a "Tool" that anyone on the
team can use safely.

---

## 5. Security and Compliance: The `submitter` Rule

In a high-security environment (like Banking), you can't have just
"Anyone" click the button.
You want to ensure that only an authorized manager can approve a
production deployment.

### The `submitter` Option:
```groovy
input message: 'Deploy?', submitter: 'release-managers'
```

If a junior developer tries to click the button, Jenkins will say:
"Access Denied. You are not in the 'release-managers' group."
This provides a "Hard Guardrail" that meets the security requirements
of the most demanding AWS enterprises.

---

## Summary

- The input step allows a pipeline to pause and wait for human intervention before proceeding
- Using input gates is the standard way to implement Continuous Delivery with manual safeguards
- Jenkins releases the build agent during the input pause, saving on AWS compute costs
- Parameterized inputs allow users to provide dynamic data, such as version tags or target regions
- The submitter parameter enforces security by restricting approval rights to specific users or groups
- Wrapping the input step in a timeout is a senior-level best practice to prevent indefinite hanging
- Input steps transform a rigid automation script into a flexible, team-friendly self-service tool

In the next file, `03-parameters-directive.md`, you will learn how to gather information from a user at the very start of a build.
