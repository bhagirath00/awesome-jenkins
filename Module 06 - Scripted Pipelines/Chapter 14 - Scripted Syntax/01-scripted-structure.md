# Scripted Pipeline Structure: The Raw Power of Groovy

## 1. Introduction: The Artist and the Blank Canvas

Imagine you are a world-class painter.
For years, you have been working with "Paint-by-Numbers" kits.
These kits are wonderful. They provide the canvas, the colors are
already mixed, and every area is clearly marked with a number.
If you follow the numbers, you are guaranteed to create a beautiful,
coherent picture.
This is the **Declarative Pipeline**.
It is safe, it is structured, and it is almost impossible to fail.

But today, you feel a need for more.
You want to create something that has never been seen before.
You want to mix your own colors. You want to use a different type
of brush. You want to paint outside the lines.
You put away the kit and you pick up a **Blank Canvas**.
With a blank canvas, there are no numbers. There are no rules.
The only limit is your own imagination and your skill with the brush.
This is the **Scripted Pipeline**.

In the world of Jenkins and DevOps, the Scripted Pipeline is the
original, high-performance way of writing automation.
It is a raw **Groovy script** that has direct access to the Jenkins API.
While Declarative is the "Safe Standard" for 90% of jobs, Scripted
is the "Secret Weapon" for the most complex, dynamic, and "Edge-Case"
problems that a DevOps professional will ever face.
In this file, we are going to go into **absolute, exhaustive technical depth**
on the structure of a Scripted pipeline.
We will look at the `node` block, the flow of raw code, and the
senior-level philosophy of when to leave the "Numbers" behind and
embrace the power of the blank canvas.

---

## 2. The Root of Power: The `node` Block

In a Scripted Pipeline, there is no `pipeline` block.
Instead, the fundamental unit of structure is the **`node`** block.

### What is a `node`?
A `node` is a command that tells Jenkins: "Stop what you are doing, find
me an available worker machine (Agent) on AWS, and execute the
following code inside it."
It is the equivalent of a "Lease" on a piece of hardware.

### The Syntax of the Scripted Root:
```groovy
node('linux-builder') {
    // Everything inside here is raw Groovy code
    stage('Hello World') {
        echo 'I am running on a specialized Linux machine!'
    }
}
```

**The Senior Distinction:**
In Declarative, the `agent` is a setting.
In Scripted, the `node` is a **Function**.
You can have multiple `node` blocks in the same script.
You can even nest them!
This allows for architectural patterns that are impossible in Declarative.

---

## 3. The Flow of Code: Top-to-Bottom Execution

Unlike Declarative, which has a strict "Template," a Scripted pipeline
is just a script.
It runs from the first line to the last line, just like a Python or
Bash script.

### The Power of Variable Assignment:
In Scripted, you can define your own variables anywhere.
```groovy
def myVersion = "1.0." + env.BUILD_NUMBER
echo "Starting the build for version: ${myVersion}"

if (myVersion.contains("42")) {
    echo "This is the answer to the universe!"
}
```

This raw access to variables and logic allows you to build pipelines
that are "Self-Aware."
You can read a file from the disk, parse its contents, and then
dynamically decide which AWS region to deploy to based on what
you found.

---

## 4. The Stage in Scripted: A Logging Tool

In Declarative, a `stage` is a mandatory structural container.
In Scripted, a `stage` is **Optional**.
You don't *need* a stage to run code.

### Why do we still use them?
A senior engineer uses stages in Scripted pipelines for **Visualization**.
If you don't use a stage, your whole script will look like one giant,
nameless task in the Jenkins UI.
By wrapping your code in `stage('Build') { ... }`, you are giving
the Jenkins dashboard a "Hint" about how to show your progress.
It is a tool for humans, not for the machine.

---

## 5. Comparison: Scripted Structure vs. Declarative

| Feature | Scripted Pipeline | Declarative Pipeline |
| :--- | :--- | :--- |
| **Mandatory Block** | `node { ... }` | `pipeline { ... }` |
| **Logic** | **Raw Groovy (Unlimited)** | Limited (Directives only). |
| **Structure** | Loose and dynamic. | **Strict and predefined.** |
| **Safety** | Low (Runtime errors). | **High (Pre-build check).** |
| **Senior Rating** | For complex custom logic. | **For standard CI/CD.** |

---

## Summary

- Scripted Pipelines provide a raw, Groovy-based "blank canvas" for unlimited automation flexibility
- The fundamental unit of structure is the 'node' block, which leases a worker from the Jenkins cluster
- Unlike Declarative, Scripted pipelines follow a top-to-bottom execution flow like a standard script
- Variable assignment and conditional logic are handled directly in the code with no restrictive directives
- Stages in Scripted pipelines are optional and serve primarily as visualization markers for the UI
- Scripted syntax allows for complex architectural patterns like nested nodes and dynamic stage creation
- This approach is the "secret weapon" for solving edge-case problems that a standard template cannot handle

In the next file, `02-groovy-basics.md`, you will learn the fundamental rules of the Groovy language as it applies to Jenkins Pipelines.
