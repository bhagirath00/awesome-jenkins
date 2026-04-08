# Defining Pipeline Stages: The Roadmap of Automation

## 1. Introduction: The Marathon and the Mile Markers

Imagine you are running a 26.2-mile marathon.
If you just started running and had no idea how far you had gone or
how much further you had to go, you would eventually lose your mind.
The psychological weight of "The Unknown" is too much for most people
to handle.
That is why marathon organizers put up **Mile Markers**.

Every mile, you see a sign: "Mile 10," "Mile 20," "5 Miles to Go."
These markers don't make you run faster, but they give you a sense of
progress, a sense of control, and a way to measure your performance.
If you find that Mile 15 took you twice as long as Mile 14, you know
exactly where you started to get tired.

In the world of Jenkins Pipelines, **Stages** are those mile markers.
A pipeline without stages is just a long, boring list of text commands
running in a black box.
If it fails, you have to read the whole log to find out where it died.
But a pipeline with clear, logical stages provides a "Visual Roadmap"
of your entire software delivery process.
It allows everyone—from the junior developer to the senior manager—to
see exactly where the code is and how it is performing.

In this file, we are going to go into **extreme technical depth** on how
to define and manage stages in your Jenkinsfile.
We will look at the syntax of the `stage` block, the psychology of
good stage naming, and how to use the "Stage View" UI to troubleshoot
your production AWS deployments like a senior engineer.

---

## 2. The Anatomy of a Stage Block

In a Declarative Pipeline, a stage is a specific block of code that
lives inside the `stages { }` section.
It is the "Container" for your work.

### The Basic Syntax:
```groovy
stage('My Stage Name') {
    steps {
        // Your commands go here
        echo 'Doing some work...'
    }
}
```

### The Senior Breakdown:
1.  **The Name:**
    The string inside the parentheses is the name that will appear on
    the Jenkins dashboard.
    - **Rule:** Keep it short but descriptive.
    - **Bad:** `stage('Step 1')`
    - **Good:** `stage('Quality Gate: SonarQube')`

2.  **The 'steps' Block:**
    Inside every stage, you MUST have a `steps` block.
    This is where the actual work happens.
    You cannot put raw commands directly inside a `stage` block without
    wrapping them in `steps`.
    This is one of the "Safety Rails" of Declarative Pipeline that
    prevents accidental errors.

---

## 3. The Psychology of Great Stage Naming

A senior DevOps engineer doesn't just name stages for the machine;
they name them for the **Humans** who use the machine.

### The "Status at a Glance" Principle:
When a build fails, the manager shouldn't have to call you to ask
"What happened?".
They should be able to look at the Jenkins dashboard and see a red
box on a stage named **"AWS Production Deploy."**
Immediately, they know the scope of the disaster.

### Professional Stage Name Examples:
-   `stage('Source: Git Checkout')`
-   `stage('Build: Maven Compile')`
-   `stage('Test: Unit & Integration')`
-   `stage('Security: Vulnerability Scan')`
-   `stage('Release: Docker Push to ECR')`
-   `stage('Deploy: Staging Environment')`
-   `stage('Approval: Manual Gate')`

---

## 4. The Stage View UI: Your Command Center

One of the greatest benefits of using stages is the **Stage View**
on the Jenkins job page.
It is a grid that shows every build and every stage.

### How to Read the Grid:
- **Rows:** Each row represents a single build (e.g., #42, #43).
- **Columns:** Each column represents a specific stage.
- **Colors:**
    - **Green:** The stage finished successfully.
    - **Red:** The stage failed.
    - **Grey:** The stage was skipped (perhaps because a previous
      stage failed).

### The "Hover" Trick:
If you hover your mouse over a specific box in the grid, Jenkins will
show you the "Logs" for just that stage.
This is a massive time-saver!
Instead of searching through a 10,000-line log file, you can jump
directly to the 5 lines of code that caused the failure in that stage.

---

## 5. Advanced Stage Strategies: The "When" Clause

Sometimes, you don't want to run every stage every time.
Maybe you only want to run the "Deploy" stage if the code is on the
`main` branch.
A junior engineer would use a complex `if/else` statement inside the steps.
A senior engineer uses the **`when`** directive.

### Example: Branch-Specific Deployment
```groovy
stage('Deploy to Prod') {
    when {
        branch 'main'
    }
    steps {
        echo 'Deploying to the live AWS environment...'
        sh './deploy-prod.sh'
    }
}
```

### Why the 'when' clause is better:
- **Readability:** Anyone can look at the file and immediately see the
  rules for that stage.
- **Clean Dashboard:** If the condition is not met, Jenkins will simply
  "Skip" the stage and show it as a light grey box.
- **Safety:** It prevents accidental deployments from feature branches.

---

## 6. Comparison: Small Stages vs. Big Stages

| Feature | Many Small Stages | One Giant Stage |
| :--- | :--- | :--- |
| **Visibility** | **Excellent** | Poor |
| **Troubleshooting** | **Easy (Isolated)** | Hard (Need to read whole log) |
| **Dashboard** | Detailed Roadmap | Just one big box |
| **Performance** | Slightly slower (Overhead) | Faster |
| **Senior Choice** | **Highly Recommended** | Discouraged |

---

## Summary

- Stages are the fundamental "mile markers" that provide a visual roadmap for your pipeline
- Every stage must contain a 'steps' block where the actual work is performed
- Professional stage naming improves communication and speeds up troubleshooting
- The Stage View UI allows anyone to see the build status and progress at a glance
- Green boxes indicate success, red indicates failure, and grey indicates a skipped stage
- The 'when' directive provides a clean way to add conditional logic to specific stages
- Breaking your pipeline into many small stages is a senior-level best practice for visibility

In the next file, `02-parallel-stages.md`, you will learn how to make your pipeline run faster by executing multiple stages at the exact same time.
