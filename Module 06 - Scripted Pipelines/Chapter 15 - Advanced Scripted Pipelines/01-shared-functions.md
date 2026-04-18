# Shared Functions: The Building Blocks of Automation

## 1. Introduction: The Lego Set and the Master Builder

Imagine you are a world-class master builder working for the **Lego Group**.
You have been given a massive project: build a life-sized model of
the Empire State Building.
This model is going to be made of 500,000 individual Lego bricks.

As a master builder, you don't just start snapping random bricks
together for 40 hours straight.
If you did, you would eventually lose track of where you are, you
would make a mistake in the structure, and the whole building would
eventually collapse under its own weight.
Instead, you look for **Patterns**.

You notice that the windows of the building are all exactly the same.
You notice that the structural columns are all exactly the same.
So, instead of building each window one by one, you build a "Template"
for a window.
You build 1,000 copies of that same window in a separate factory.
Then, you just "Plug" them into the main building.
This template is a **Function**.
It is a reusable, modular piece of work that you can use over and
over again to build something massive and complex with total precision.

In the world of Jenkins and DevOps, **Shared Functions** are those
Lego templates.
In a Scripted pipeline, you are not restricted to the built-in commands.
You can define your own "Custom Commands."
Whether it is a function to "Deploy to S3," "Scan for Bugs," or "Notify
Slack," shared functions allow you to move from "Spaghetti Code"
to a "Modular System."
In this file, we are going to go into **absolute, exhaustive technical depth**
on how to define and use functions in Scripted pipelines.
We will look at the syntax of the `def` keyword, the philosophy of
"DRY" (Don't Repeat Yourself), and the senior-level strategies for
building a library of automation tools that your entire company can use.

---

## 2. The Philosophy of "DRY" (Don't Repeat Yourself)

As a senior architect, the greatest sin you can commit is **Copy-Paste**.
If you have 10 different pipelines that all use the exact same
20 lines of code to log into AWS, you have a major problem.

### The Problem with Duplication:
If Amazon changes their security API tomorrow, you have to find all
10 of those pipelines and update them manually.
You will forget one. One of your builds will fail.
You will spend 4 hours debugging it.

### The Senior Solution:
You take those 20 lines of code and you wrap them in a **Function**.
You put that function in a central place.
Now, all 10 pipelines just call `awsLogin()`.
If the API changes, you update the function **Once**, and all 10
pipelines are fixed instantly.
This is the power of the "Lego Brick" approach.

---

## 3. Defining a Custom Function: The Syntax

In a Scripted Pipeline, you define a function using the `def` keyword.
Functions usually live at the very bottom of your `Jenkinsfile`,
outside of the `node` block.

### The Senior Implementation:
```groovy
node('aws-builder') {
    stage('Deploy') {
        // Calling our custom function
        deployToAmazon('production', 'us-east-1')
    }
}

// Our custom "Lego Brick"
def deployToAmazon(String envName, String region) {
    echo "Initiating deployment to the ${envName} environment in ${region}..."
    sh "aws s3 sync ./dist s3://my-app-${envName}-${region}"
    echo "Deployment to ${envName} is complete."
}
```

**The Senior Distinction:**
-   **Arguments:** Notice how we pass `envName` and `region`.
    This makes the function "Generic." It can be used for any environment.
-   **Separation of Concerns:** The main pipeline handles the "Flow."
    The function handles the "Details."

---

## 4. Returning Data: The Intelligent Function

A function doesn't just have to "Do" things; it can also "Tell" things.
For example, you can build a function that calculates the "Health Score"
of a deployment and returns it to the main script.

### The Senior Implementation:
```groovy
def getHealthScore() {
    def result = sh(script: './check-health.sh', returnStdout: true).trim()
    return result.toInteger()
}

node {
    def score = getHealthScore()
    if (score < 80) {
        error "Health score is too low (${score}). Aborting!"
    }
}
```

This allows you to build pipelines that make **Mathematical Decisions**
about whether to proceed or rollback.

---

## 5. Summary Table: Why Seniors Use Functions

| Benefit | How it helps | Why it saves money |
| :--- | :--- | :--- |
| **Reusability** | Build once, use 1,000 times. | Saves thousands of developer hours. |
| **Maintainability** | Update once, fix 1,000 times. | Prevents "Ghost" bugs in old code. |
| **Readability** | Hides complex logic in simple names. | Makes it easy for juniors to understand. |
| **Testing** | You can test a function in isolation. | Reduces the risk of production crashes. |

---

## Summary

- Shared functions are the modular building blocks that transform spaghetti code into a clean system
- The 'def' keyword is used to define custom, reusable logic outside of the main node block
- Following the DRY (Don't Repeat Yourself) principle reduces maintenance overhead and technical debt
- Passing arguments to functions makes them generic and adaptable to different AWS environments
- Functions can return data, allowing the main pipeline to make intelligent, math-based decisions
- Organizing custom methods at the bottom of the Jenkinsfile keeps the primary roadmap readable
- Modular automation engineering is the key to scaling CI/CD across thousands of repositories

In the next file, `02-dynamic-stages.md`, you will learn how to combine functions with loops to build pipelines that adapt their structure in real-time.
