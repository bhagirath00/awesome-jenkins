# The Parameters Directive: Building Self-Service Tools

## 1. Introduction: The Vending Machine Analogy

Imagine you are walking through a busy airport and you are incredibly
thirsty. You see a **Vending Machine** against the wall.
The vending machine is an automated system.
It knows how to hold drinks, it knows how to take money, and it knows
how to dispense the product into your hand.
But the machine doesn't just "Give you a random drink."

Instead, it presents you with a **Selection Panel**.
You can choose a Soda, a Water, or a Juice.
You can choose if you want it cold or room temperature.
You can even choose how many you want to buy.
The vending machine is a "Parameterized Automation."
It provides a standard service, but it allows the **User** to customize
the "Outcome" based on their specific needs at that specific moment.

In the world of Jenkins and DevOps, the **Parameters Directive** is that
selection panel. It allows you to build a single, powerful pipeline
that can behave differently depending on what the user wants.
Instead of having 10 different pipelines for 10 different AWS regions,
you have one pipeline with a "Region" parameter.
Instead of having a separate job for "Small," "Medium," and "Large"
deployment types, you have one job with a "Size" parameter.

A senior DevOps engineer knows that the goal of automation is to
**Empower the Team**.
By building self-service, parameterized pipelines, you allow your
developers, QA engineers, and managers to perform complex tasks safely
on the AWS cloud without needing to call you for help every time.
In this file, we are going to go into **absolute, exhaustive technical depth**
on the parameters directive.
We will look at every type of parameter available, how to access them
in your code, and the senior-level best practices for building a
"User-Friendly" automation dashboard.

---

## 2. Why Use Parameters? (The Three Pillars)

As a senior architect, you don't just add parameters because they look cool.
You add them to solve three specific problems.

### Pillar 1: DRY (Don't Repeat Yourself)
If you have 5 jobs that all do the same thing but with different names,
you have a maintenance nightmare.
If you need to change a security setting, you have to change it 5 times.
Parameters allow you to consolidate those 5 jobs into one single,
high-quality source of truth.

### Pillar 2: Safety and Guardrails
Parameters allow you to restrict what a human can do.
Instead of letting a developer type a random string for the deployment
target (where they might make a typo like `prouction`), you give them
a **Choice Parameter** with only two options: `Staging` and `Production`.
The machine enforces the rules.

### Pillar 3: Traceability
When a parameterized build runs, Jenkins records exactly what the user
selected.
In the build history, you can see: "Build #45 was run by Sarah with
the parameter `ENV=Production`."
This provides a perfect audit trail for your AWS infrastructure.

---

## 3. The Syntax: Defining the Panel

In a Declarative Pipeline, the `parameters { }` block lives at the
very top of the file.

### The Senior Implementation:
```groovy
pipeline {
    agent any
    parameters {
        // A simple text box
        string(name: 'PROJECT_NAME', defaultValue: 'Core-API', description: 'What are we building?')
        
        // A dropdown menu (The safest choice)
        choice(name: 'TARGET_AWS_REGION', choices: ['us-east-1', 'eu-west-1', 'ap-south-1'], description: 'Deploy to which region?')
        
        // A checkbox
        booleanParam(name: 'RUN_SECURITY_SCAN', defaultValue: true, description: 'Should we run Snyk?')
        
        // A secret text box (Masked in the UI)
        password(name: 'DB_ADMIN_KEY', description: 'Provide the temporary DB key')
    }
    stages {
        stage('Deploy') {
            steps {
                // Accessing the parameters using the 'params' object
                echo "Starting build for ${params.PROJECT_NAME}"
                echo "Targeting region: ${params.TARGET_AWS_REGION}"
            }
        }
    }
}
```

---

## 4. Parameter Types Reference Table

| Type | Best For... | Senior Tip |
| :--- | :--- | :--- |
| **`string`** | Names, tags, IDs. | Always provide a sane `defaultValue`. |
| **`choice`** | **Standardization.** | Use this for 90% of your parameters. |
| **`booleanParam`**| Feature toggles. | Great for "Optional" stages like extra testing. |
| **`password`** | One-time keys. | Never use this for permanent secrets. |
| **`text`** | Multi-line config. | Useful for passing small JSON documents. |

---

## 5. Senior Strategy: The "Default" Trap

There is one common mistake that junior engineers make with parameters.
They forget that the **First Time** a pipeline is run after you add
parameters, Jenkins doesn't know about them yet.

### The Workflow:
1.  You push your new `Jenkinsfile` with the `parameters` block.
2.  You click "Build Now."
3.  Jenkins runs the job, discovers the parameters, and **then**
    updates the UI.
4.  **The Result:** The first build always uses the default values.
    You must wait for the first build to finish before you see the
    "Build with Parameters" button in the menu.
A senior engineer tells their team: "The first run is just for the
machine to learn the new rules. The second run is where you make
your choices."

---

## Summary

- Parameters transform a static pipeline into a dynamic, self-service tool for the entire team
- The parameters directive is used to define input fields like strings, choices, and checkboxes
- Choice parameters are a senior best practice for preventing typos and enforcing standardization
- Parameters are accessed in the pipeline code using the global 'params' object
- Building parameterized pipelines follows the DRY principle by consolidating multiple jobs into one
- Every parameter should include a clear description and a sane default value for usability
- The first run of a new pipeline is required for Jenkins to "discover" and display the parameters

In the next file, `04-post-directive.md`, you will learn how to handle cleanup and reporting using the post directive.
