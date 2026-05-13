# Deploy to AWS: Reaching the Summit

## 1. Introduction: The Mountain Climber and the Support Team

Imagine you are a **Mountain Climber** attempting to climb **Mount
Everest**.
You are the one who is actually on the mountain, battling the wind
and the snow (the **Application Code**).
But as a climber, you can't reach the summit alone.
You rely on a **Support Team** back at the base camp.

The support team provides the oxygen. They provide the maps. They
provide the radio communications.
When you are ready to move from Camp 3 to Camp 4, you check with
the support team to make sure the weather is clear and the route
is safe.
Once you reach the summit (the **Production Environment**), the
support team celebrates your success and records it for the world
to see.

In the world of DevOps, **Deploying to AWS** is that final push to the
summit.
Jenkins is your "Support Team."
It takes your "Proven" code and "Moves" it onto the massive mountain
of AWS.
It handles the connection, it verifies the environment, and it
ensures that your code reaches the summit safely and securely.

## 2. The AWS Steps Plugin: The Senior Shortcut

A senior DevOps engineer uses the **AWS Steps Plugin** to make the
deployment "Cloud-Native."

### Key Features:
-   **`withAWS` Block:** Automatically handles your Access Keys and
    Secret Keys. It ensures you are logged in only for the duration
    of the deployment.
-   **S3 Upload/Download:** Specialized, high-speed commands for
    moving files to and from Amazon S3.
-   **Lambda Invocation:** You can tell Jenkins to "Trigger" an AWS
    Lambda function to perform a specialized task (like clearing a
    cache or updating a database).

---

## 3. Deployment Patterns on AWS

A master builder doesn't just "Copy Files." They use a **Pattern**.

-   **Blue/Green Deployment:** Jenkins launches a "New" version of your
    app (Green) alongside the "Old" version (Blue). Once you are
    sure the new one is perfect, Jenkins tells the AWS Load Balancer
    to switch the traffic.
-   **Canary Deployment:** Jenkins sends only 5% of the traffic to
    the new version. If everything is fine for 10 minutes, it sends
    the other 95%.

These patterns, orchestrated by Jenkins, ensure that a "Buggy Build"
never results in a "Service Outage" for your customers.

---

## Summary

- Deploying to AWS is the final "push to the summit" for verified application code
- The AWS Steps plugin provides cloud-native helpers for secure and high-speed deployments
- The withAWS block manages security credentials automatically during the execution phase
- S3 integration allows for efficient storage and retrieval of build artifacts in the cloud
- Blue/Green deployment patterns minimize downtime by switching traffic between environments
- Canary deployments provide a "safety valve" by testing new code on a small user group first
- Orchestrating these patterns through Jenkins ensures a reliable and professional AWS presence

In the next file, `02-deploy-to-kubernetes.md`, you will learn how to orchestrate these deployments in a containerized cluster.
