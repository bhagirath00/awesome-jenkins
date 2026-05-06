# Authorization Strategies: Defining the Permissions

## 1. Introduction: The High-Rise Hotel

Imagine you have just checked into a **Luxury High-Rise Hotel**.
The hotel has many different areas.
There is the lobby, there is the pool, there is your private room
on the 42nd floor, and there is the penthouse suite on the roof.
There is also a laundry room in the basement and a kitchen for
the staff.

You have a **Keycard**.
Your keycard is your "Authorization."
When you swipe it at the elevator, the elevator knows you are
allowed to go to the 42nd floor, but it **forbids** you from going
to the penthouse.
It allows you to enter the pool, but it forbids you from entering
the kitchen.
The keycard defines the "Limits" of your power within the hotel.

In the world of Jenkins, **Authorization Strategies** are those
keycards.
Once a user has been "Authenticated" (they have entered the lobby),
the authorization strategy defines exactly what they are allowed
to see and do.
A senior DevOps engineer knows that "Giving Everyone Admin Power"
is a recipe for disaster. Instead, you follow the **Principle of
Least Privilege**.

## 2. The Principle of Least Privilege

This is the golden rule of a senior security architect.
**Every user should have the absolute minimum amount of power
they need to do their job.**

-   **Junior Dev:** Should be able to "Build" a job, but not "Configure"
    it or "Delete" it.
-   **QA Engineer:** Should be able to "Read" the test results, but not
    access the production AWS credentials.
-   **SRE/Admin:** Has full control, but only uses that power when
    performing a specific maintenance task.

By restricting power, you ensure that an "Accident" (like a developer
deleting the wrong job) or a "Malicious Act" (like a hacker trying to
steal credentials) is stopped before it can do damage.

---

## 3. The Role-Based Access Control (RBAC) Strategy

The most professional way to manage authorization in a large company
is the **Role-Based Access Control (RBAC)** plugin.

Instead of giving permissions to "Individual People" (which is too much
work), you create **Roles**.
1.  **"Developer" Role:** Can build and see logs.
2.  **"Manager" Role:** Can approve deployments and see reports.
3.  **"Architect" Role:** Can change the AWS settings and manage plugins.

You then "Assign" your users or LDAP groups to these roles.
If a new developer joins the team, you just give them the "Developer"
role, and they instantly have all the correct permissions.

---

## Summary

- Authorization defines the specific actions and areas a user can access after logging in
- The Principle of Least Privilege ensures users only have the minimum power required for their role
- Restricting permissions prevents accidental deletions and limits the impact of a security breach
- Role-Based Access Control (RBAC) is the enterprise standard for managing permissions at scale
- Roles like "Developer," "QA," and "Admin" provide a structured way to assign rights to groups
- Centralized permission management is far more efficient than assigning rights to individuals
- Effective authorization strategies are essential for maintaining compliance and production stability

In the next file, `04-csrf-protection.md`, you will learn how to protect Jenkins from specialized "Web Attacks."
