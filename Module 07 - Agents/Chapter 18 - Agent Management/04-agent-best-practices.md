# Agent Best Practices: The Senior Architect's Rules

## 1. Introduction: The Professional Workshop

Imagine you are visiting the workshop of a **Master Carpenter**.
You expect to see certain things.
You expect to see the tools organized perfectly on the wall.
You expect to see the floor swept and free of sawdust.
You expect to see "Safety Rules" posted clearly on the door.
A professional workshop is not just a place where work happens;
it is a place where work happens **Efficiently**, **Safely**, and
**Repeatably**.

The master carpenter doesn't waste time looking for their hammer.
They don't trip over a pile of scrap wood.
They have a "System" for managing their environment.

In the world of Jenkins and AWS, your worker agents are your
**Professional Workshop**.
If your agents are messy, unorganized, and unreliable, your CI/CD
"Furniture" will be crooked and weak.
In this final file of Module 7, we are going to list the **Senior Rules**
for managing your Jenkins agents like a master architect.

## 2. Rule 1: The "No-Work-on-Controller" Law

This is the most important rule in all of Jenkins.
**Never, ever run builds on the Controller.**
The controller is the "Brain." If the brain is busy compiling code,
it can't manage the rest of the body.
- **Senior Tip:** Set the number of "Executors" on the controller
  to **Zero**. This forces every single job to run on a specialized agent.

## 3. Rule 2: The "Hygienic" Workspace

Builds create garbage. If you don't clean it up, the workshop becomes
dangerous.
- **Senior Tip:** Use the `cleanWs()` command in your `post { always { ... } }`
  block.
- **Senior Tip:** Set up a "Cron Job" to delete any workspaces that
  haven't been used in 7 days. This keeps your AWS EC2 disks clean
  automatically.

## 4. Rule 3: The "Stateless" Philosophy

An agent should be like a **Disposable Glove**.
You use it for one task, and then you should be able to throw it away
and get a new one without any problems.
- **Senior Tip:** Never store "Permanent Data" on an agent's disk.
- If you need to save a file, upload it to **Amazon S3**.
- If you need to save a configuration, put it in **Git**.
- This allows you to delete and recreate your entire worker fleet in
  minutes if a security problem occurs.

---

## Summary

- The "No-Work-on-Controller" law preserves the health and responsiveness of the Jenkins brain
- Setting controller executors to zero is a mandatory practice for enterprise-scale stability
- Maintaining hygienic workspaces through automated cleanup prevents disk-related build crashes
- The stateless philosophy ensures that agents can be replaced or scaled without data loss
- All permanent artifacts and configurations should be stored in S3 or Git, not on local disks
- Well-organized agent labels (from Chapter 16) are essential for efficient workload routing
- Professional agent management is the difference between a "fragile script" and a "robust platform"

You have now completed **Module 7: Agents**. You have mastered the architecture, the types, and the management of the modern Jenkins workforce.

In the next Module, **Module 8: Plugins**, you will learn how to expand the "Brain" of Jenkins with thousands of specialized tools and integrations.
