# Role-Based Access Control (RBAC): The Structured Hierarchy

## 1. Introduction: The Corporate Office Building

Imagine you are the **Chief Security Officer** of a massive, 100-story
corporate skyscraper.
Thousands of people enter this building every day.
You have the CEO, the janitors, the interns, the security guards,
and the visiting clients.

You don't want to personally talk to every single person and tell
them which floor they are allowed to go to.
That would be an impossible task.
Instead, you create a **Structured Hierarchy**.
You define a set of **Roles**.
-   **"The Executive Role":** Has a keycard that opens every door
    in the building.
-   **"The Maintenance Role":** Has a keycard that opens the laundry,
    the mechanical rooms, and the service elevators.
-   **"The Employee Role":** Has a keycard that only opens their
    specific office and the cafeteria.
-   **"The Visitor Role":** Has a keycard that only opens the lobby
    and the meeting rooms.

When a new person joins the company, you don't ask "What can they
do?" Instead, you ask **"What is their role?"**
You give them the correct keycard, and the building's computer system
handles the rest.

In the world of Jenkins, **Role-Based Access Control (RBAC)** is that
skyscraper's security system.
It is the most professional and scalable way to manage permissions
across a massive organization.

## 2. Global Roles vs. Project Roles

A senior architect uses a two-layered approach to RBAC.

### Layer 1: Global Roles
These roles define "Company-Wide" powers.
-   **Admin:** Full power.
-   **Read-Only:** Can see everything but change nothing.
These are great for managers and auditors who need to oversee the
entire system.

### Layer 2: Item (Project) Roles
These roles are restricted to a **Specific Folder** or **Job Name**.
-   **Project-Alpha-Dev:** Can build any job that starts with
    `alpha-*`.
-   **Project-Beta-QA:** Can see the logs for anything in the
    `/beta/` folder.
This is where the "Principle of Least Privilege" is truly enforced.

---

## 3. The Power of "Regex" Filtering

The RBAC plugin is "Smart."
Instead of picking every single job one-by-one, you use a **Pattern**.
For example, you can say: "This role has power over any job that
matches the pattern `^ecommerce-.*`."
This means if you create a new job called `ecommerce-payment`
tomorrow, the correct people will **instantly** have permission to
work on it without you doing anything.
This "Automation of Security" is the mark of a master architect.

---

## Summary

- Role-Based Access Control (RBAC) is a structured hierarchy for managing enterprise permissions
- Global Roles define broad powers across the entire Jenkins controller and its agents
- Item Roles restrict access to specific projects or folders, enforcing least privilege
- Using Regular Expressions (Regex) allows for the automated assignment of rights to new jobs
- RBAC eliminates the need to manage permissions for thousands of individual users manually
- The plugin provides a scalable and predictable security model for large organizations
- Structured access control is a mandatory requirement for maintaining compliance on AWS

In the next file, `02-audit-logging.md`, you will learn how to keep a perfect record of everything that happens in your skyscraper.
