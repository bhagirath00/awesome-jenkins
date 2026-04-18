# Error Handling in Scripted Pipelines: The Resilient Engine

## 1. Introduction: The Pilot and the Engine Failure

Imagine you are a world-class airline pilot flying a massive passenger
jet from London to New York.
The flight is going perfectly. The sun is shining, the passengers
are sleeping, and the engines are humming.
Then, suddenly, the right engine explodes.
The plane shudders, alarms start screaming in the cockpit, and the
autopilot disconnects.

A junior pilot would panic. They would freeze at the controls and
hope that the problem "Fixes itself."
A senior pilot, however, has spent thousands of hours in the simulator
preparing for exactly this moment.
They don't panic. They immediately execute a "Condition Checklist."
First, they stabilize the aircraft.
Second, they shut down the fuel to the broken engine to prevent a fire.
Third, they re-route the power to the remaining systems.
Fourth, they inform the passengers and the ground control about the
situation.
The engine failure is a "Disaster," but because the pilot has a
**Plan for Failure**, the plane still lands safely.

In the world of Jenkins and DevOps, **Error Handling** is that checklist.
In a Scripted pipeline, there are no "Safety Rails."
If a command fails, the entire script crashes instantly.
If your deployment to AWS fails in the middle of a database update,
you could leave your company's data in a "Corrupted" state.
A senior DevOps engineer knows that "Failure is Guaranteed."
By using the **Try/Catch/Finally** logic of the Groovy language,
you build a pipeline that is "Resilient."
You build a system that can detect a disaster, clean up the mess,
and notify the team, all without a single second of manual effort.

In this file, we are going to go into **absolute, exhaustive technical depth**
on the mechanics of error handling in Scripted pipelines.
We will look at how to "Catch" an exception, how to perform "Emergency
Rollbacks" on AWS, and how to ensure that your workers are always
cleaned up, no matter how bad the disaster was.

---

## 2. The Logic of Resilience: Try, Catch, Finally

In a Scripted Pipeline, we use the standard Groovy error-handling
structure. This is the #1 difference between a "Script" and a "System."

### A. The `try` Block (The "Mission")
This is where you put your actual work. You are saying: "Try to
execute this task, but watch it closely."
- **Example:** `sh './deploy-to-aws.sh'`

### B. The `catch` Block (The "Emergency Response")
If anything inside the `try` block fails (throws an error), the script
immediately jumps into the `catch` block.
The "Mission" stops, and the "Response" begins.
- **Senior Use Case:** This is where you send a Slack alert or run
  an automated `kubectl rollout undo` command.

### C. The `finally` Block (The "Cleanup")
The code inside the `finally` block **Always** runs, whether the
mission succeeded or failed.
- **Senior Use Case:** This is where you delete temporary files,
  stop Docker containers, or release AWS resource locks.

---

## 3. Senior Implementation Example: The Safe Deploy

Let's look at a professional Scripted pipeline that uses this logic
to protect an AWS deployment.

```groovy
node('aws-builder') {
    try {
        stage('Deploy') {
            echo 'Initiating deployment to Amazon EKS...'
            // If this command fails, we catch the error
            sh 'kubectl apply -f deployment.yaml'
        }
        stage('Verify') {
            echo 'Verifying the health of the new containers...'
            sh 'kubectl rollout status deployment/myapp --timeout=2m'
        }
    } catch (Exception err) {
        // The disaster response
        echo "DISASTER DETECTED: ${err.message}"
        stage('Rollback') {
            echo 'Rolling back to the previous stable version...'
            sh 'kubectl rollout undo deployment/myapp'
        }
        // Telling the team
        slackSend color: 'danger', message: "DEPLOYMENT FAILED: Rollback initiated."
        // We must re-throw the error so Jenkins knows the build failed
        throw err
    } finally {
        // The hygiene
        stage('Cleanup') {
            echo 'Removing temporary artifacts from the worker...'
            cleanWs()
        }
    }
}
```

---

## 4. Why we "Throw" the Error Again?

A common mistake for junior engineers is to "Catch" an error and then
just stop.
If you do this, Jenkins will see that the `catch` block finished
successfully and it will mark the whole build as **GREEN (Success)**.
This is a disaster!
You have a failed deployment, but your dashboard says everything is
perfect.

**The Senior Rule:**
Always use the `throw` keyword at the end of your `catch` block.
This tells Jenkins: "I've handled the emergency and cleaned up the
mess, but please remember that the original task failed."
This ensures that your "Success Rate" statistics stay honest and
accurate.

---

## Summary

- Error handling is the "checklist" that allows Scripted pipelines to recover from disasters
- The try block contains the main work, while the catch block handles emergency responses
- The finally block is reserved for mandatory cleanup tasks that must run regardless of outcome
- Senior engineers use the catch block to trigger automated rollbacks on AWS resources
- It is critical to re-throw exceptions at the end of a catch block to ensure the build status is accurate
- Automated hygiene in the finally block prevents disk leaks and resource exhaustion
- Mastering try-catch-finally transforms a fragile script into a resilient, production-grade system

In the next chapter, `Chapter 15: Advanced Scripted Pipelines`, you will learn about loops, custom functions, and the "God Mode" of Jenkins: Shared Libraries.
