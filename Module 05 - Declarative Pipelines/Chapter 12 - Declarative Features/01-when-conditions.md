# When Conditions: The Intelligence of Choice

## 1. Introduction: The Guard at the Gate

Imagine you are a guard standing at the front gate of a high-security
military base.
You have a set of strict orders.
If someone approaches the gate in a military vehicle and they have the
correct ID badge, you let them through immediately.
If someone approaches on foot but they have a "VIP" visitor pass,
you also let them through.
However, if someone approaches in a civilian car with no ID, you
immediately block them and call for backup.

Your behavior as a guard is not "Static."
It is not the same for everyone.
Instead, your behavior is **Conditional**.
It changes based on the "Context" of the person standing in front
of you.
You are making intelligent decisions based on a set of rules.

In the world of Jenkins Pipelines, **When Conditions** are those rules.
They allow you to add "Intelligence" to your stages.
A junior engineer writes a pipeline that runs every single stage
every single time.
This is inefficient and dangerous.
A senior DevOps engineer writes a pipeline that says:
"Only run the 'Production Deploy' stage if the code is on the `main`
branch AND the unit tests have passed AND a human has given approval."
By mastering the `when` directive, you create a "Dynamic" automation
system that adapts to the situation, saving you time, money, and preventing
disastrous deployments to the AWS cloud.

In this file, we are going to go into **absolute, exhaustive technical depth**
on the `when` directive.
We will look at the different "Conditions" available, how to combine
them with logical operators (AND, OR, NOT), and how to use them
to build a sophisticated, branch-aware CI/CD roadmap.

---

## 2. The Philosophy of the `when` Directive

The `when` directive allows you to skip a stage based on the state
of the environment.
It is the primary way we handle **Multi-Branch Workflows** in a
professional environment.

### Why use `when` instead of an `if` statement?
1.  **Visibility:**
    If a stage is skipped because of a `when` condition, Jenkins shows
    it as a light-grey box on the dashboard.
    It tells the team: "I skipped this on purpose because the rules
    were not met."
    If you use a Groovy `if` statement inside the steps, the stage
    will look like it "Ran" but did nothing, which is confusing.

2.  **Cleanliness:**
    The `when` directive lives at the top of the stage, separate from
    the actual commands.
    It is much easier to read "Run when branch is main" than to search
    through a 50-line shell script for a conditional check.

---

## 3. Common Conditions: The Senior Toolkit

Jenkins provides a rich set of built-in conditions that you can use
in your pipelines.

### A. Branch Conditioning
This is the #1 most common condition.
```groovy
stage('Deploy to Prod') {
    when {
        branch 'main'
    }
    steps {
        echo 'Deploying...'
    }
}
```
**Senior Tip:** You can also use wildcards like `branch 'feature/*'`
to run a stage on every feature branch but skip it on `main`.

### B. Environment Variable Conditioning
Sometimes you want to run a stage based on a variable.
```groovy
stage('Debug Logs') {
    when {
        environment name: 'DEBUG_MODE', value: 'true'
    }
    steps {
        sh './collect-debug-data.sh'
    }
}
```

### C. Tag Conditioning
In professional release management, we often use Git Tags (like `v1.0.1`).
```groovy
stage('Release to ECR') {
    when {
        buildingTag()
    }
    steps {
        sh 'docker push my-registry/app:$TAG_NAME'
    }
}
```

---

## 4. Logical Operators: Combining the Rules

A senior engineer rarely relies on a single condition.
Usually, the rules are more complex.
To handle this, Jenkins provides three logical operators: `allOf`, `anyOf`, and `not`.

### The `allOf` Operator (Logical AND)
This requires **EVERY** condition inside it to be true.
```groovy
when {
    allOf {
        branch 'main'
        environment name: 'FORCE_DEPLOY', value: 'true'
    }
}
```

### The `anyOf` Operator (Logical OR)
This requires **AT LEAST ONE** condition to be true.
```groovy
when {
    anyOf {
        branch 'main'
        branch 'release/*'
    }
}
```

### The `not` Operator (Logical NOT)
This "Flips" the result.
```groovy
when {
    not {
        branch 'experimental/*'
    }
}
```

---

## 5. Comparison: `when` vs. `if/else`

| Feature | `when` Directive | Groovy `if/else` |
| :--- | :--- | :--- |
| **Location** | Top of the Stage (Declarative). | Inside the `steps` (Scripted). |
| **Dashboard** | Shows "Skipped" visually. | Shows "Success" (confusing). |
| **Readability** | **Extremely High.** | Low (Hidden in code). |
| **Performance** | Fast (Evaluated by Jenkins). | Slower (Requires Groovy engine). |
| **Senior Choice** | **Default for Stages.** | For complex logic inside a step. |

---

## Summary

- The 'when' directive adds intelligence to your pipeline by allowing stages to be skipped based on conditions
- Using 'when' is preferred over 'if' statements as it provides clear "Skipped" visibility on the dashboard
- Branch-based conditioning is the most common use case for separating dev, staging, and prod workflows
- Logical operators like allOf, anyOf, and not allow for the creation of complex, multi-variable rules
- Environmental conditions allow stages to run based on specific variables or the presence of a Git tag
- The 'when' directive is evaluated before the stage starts, saving time and compute resources
- Mastering conditional logic is a key requirement for building dynamic, branch-aware CI/CD systems

In the next file, `02-input-step.md`, you will learn about the 'input' step and how to implement manual approval gates in your automation.
