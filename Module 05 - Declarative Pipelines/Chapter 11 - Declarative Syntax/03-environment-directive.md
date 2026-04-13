# The Environment Directive: The Intelligence of the Machine

## 1. Introduction: The Pilot and the Flight Data

Imagine you are a world-class pilot sitting in the cockpit of a
state-of-the-art Boeing 787 Dreamliner.
You have a perfect airplane (the **Agent**).
You have a perfect flight plan (the **Stages**).
But as you are flying through the air, you need more than just the
plane and the plan.
You need **Data**.

You need to know the current air pressure outside the cabin.
You need to know the exact temperature of the engines.
You need to know the weight of the fuel in the wings and the wind speed
at your current altitude.
This data doesn't change the plane itself, but it changes how the
engines perform and how you navigate the sky.
If you don't have this "Environmental Knowledge," you are flying blind.

In the world of Jenkins and AWS, the **Environment Directive** is that
flight data. It is how you provide your workers with the "Knowledge"
they need to do their jobs correctly.
Whether it is the name of an AWS S3 bucket, a version number for your
Docker image, or a secret API token for your database, environment
variables are the "Context" that makes your pipeline dynamic and smart.
A senior DevOps engineer knows that a "Hardcoded" pipeline is a "Dead"
pipeline.
By mastering the environment directive, you create an automation system
that is flexible, secure, and ready for any situation.

In this file, we are going to go into **absolute, exhaustive technical depth**
on the philosophy and the implementation of environment variables.
We will look at the different levels of scope, the integration with
the Jenkins Credentials Manager, and the senior-level "12-Factor App"
principles that define how we manage configuration in the modern cloud.

---

## 2. The Three Scopes of Knowledge

In a Declarative Pipeline, environment variables are not all created equal.
They exist in three different "Scopes."
Understanding these scopes is the key to preventing bugs in your AWS
infrastructure.

### A. Global Scope (The "Universe" Knowledge)
Variables defined at the very top of your `pipeline` block are available
to every single stage, every step, and even every post-action.
- **Use Case:** Global constants like `PROJECT_NAME` or `AWS_REGION`.

### B. Stage Scope (The "Local" Knowledge)
Variables defined inside a specific `stage` block are only available
to that stage.
- **Use Case:** A variable like `DOCKER_IMAGE_TAG` that is only needed
  during the "Build" stage but not during the "Notification" stage.

### C. Step Scope (The "Temporary" Knowledge)
Variables defined inside a `withEnv` block or a shell script are only
available for a few seconds.
- **Use Case:** Temporary calculation values or secrets used for one
  single command.

---

## 3. The Credentials Helper: Managing Secrets

This is the most critical section for any senior engineer.
**NEVER, EVER put a password or an AWS Secret Key in your Jenkinsfile.**
If you do, you have failed the most basic security test of a DevOps professional.

### The `credentials()` Function:
Jenkins provides a built-in helper that pulls secrets from the encrypted
vault and injects them into environment variables safely.
```groovy
environment {
    // This pulls a secret named 'aws-keys' and creates two variables:
    // AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY
    AWS_CREDS = credentials('aws-production-keys')
}
```

**How it works:**
Jenkins handles the decryption and ensures that the secrets are never
printed in the build logs.
If your script tries to `echo $AWS_SECRET_ACCESS_KEY`, Jenkins will
automatically "Mask" the output with asterisks (`****`).
This is the only way to handle sensitive data in a professional AWS environment.

---

## 4. The "12-Factor" Philosophy: Config in the Environment

As you grow into a senior architect, you will hear the term **"12-Factor App."**
One of the core rules of this philosophy is: **Store config in the environment.**

### Why?
1.  **Environment Parity:**
    You use the same Jenkinsfile for "Development" and "Production."
    The only thing that changes is the environment variables.
2.  **Security:**
    Your code contains the "Logic," and the environment contains the "Secrets."
    You can share the code with the whole company, but only the
    authorized admins can see the environment secrets.
3.  **Flexibility:**
    If you need to change your AWS S3 bucket name, you don't have to
    re-write your code.
    You just change the environment variable in the Jenkins UI.

---

## 5. Built-in Environment Variables: The "Free" Knowledge

Jenkins provides a long list of variables for free.
You should use these whenever possible instead of creating your own.

| Variable | What it contains | Senior Use Case |
| :--- | :--- | :--- |
| **`BUILD_NUMBER`** | The ID of the current run. | Tagging Docker images. |
| **`BRANCH_NAME`** | The Git branch being built. | Conditional deployments. |
| **`WORKSPACE`** | The full path to the code. | Referencing files in scripts. |
| **`JENKINS_URL`** | The address of the server. | Sending links in Slack messages. |
| **`BUILD_URL`** | The link to the specific run. | Debugging failed builds. |

---

## Summary

- Environment variables provide the "intelligence" and context your pipeline workers need
- Global scope variables are available throughout the entire pipeline, from checkout to cleanup
- Stage-level environment variables allow for localized data that doesn't clutter other stages
- The credentials() helper is the ONLY professional way to inject secrets into your pipeline
- Jenkins automatically masks secret values in console logs to prevent data leakage
- Storing configuration in the environment follows the industry-standard 12-Factor App methodology
- Built-in variables like BUILD_NUMBER and BRANCH_NAME should be leveraged for consistency

In the next file, `04-options-directive.md`, you will learn how to set global policies for your pipeline, such as timeouts and log rotation.
