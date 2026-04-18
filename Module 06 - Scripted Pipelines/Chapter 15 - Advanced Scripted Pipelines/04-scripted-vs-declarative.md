# Scripted vs. Declarative: The Final Showdown

## 1. Introduction: The Pilot and the Flight Computer

Imagine you are a senior airline pilot in the year 2026.
You are flying a brand-new, ultra-modern commercial aircraft.
The cockpit has two primary modes of operation.

The first mode is the **Autopilot (Declarative)**.
In this mode, you don't touch the controls.
Instead, you interact with a high-level computer screen.
You tell the computer: "I want to fly to New York at 35,000 feet."
The computer handles all the complex math, the engine settings,
and the navigation.
It is incredibly safe, it prevents you from making mistakes,
and it is very easy to use.
It is the standard mode for 99% of your flight.

The second mode is **Manual Control (Scripted)**.
In this mode, you turn off the computer and you grab the physical
yoke and the throttle.
You have raw, direct control over every moving part of the aircraft.
If you want to do a sharp turn that the autopilot would forbid,
you can do it.
If the computer crashes and you need to land the plane yourself
in a storm, you have the power to do it.
It is much more difficult, it requires years of training, and there
are no "Safety Rails" to stop you from crashing.

In the world of Jenkins, these are your two choices.
**Declarative Pipeline** is the autopilot.
**Scripted Pipeline** is the manual control.
A senior DevOps engineer knows that you don't "Pick one and stay
with it forever."
Instead, you must master **Both**.
You use the safety of Declarative for your daily work, and you keep
the power of Scripted in your pocket for when the storm hits.
In this file, we are going to go into **absolute, exhaustive technical depth**
on the differences between these two dialects.
We will look at the philosophy, the syntax, and the senior-level
decision matrix for choosing the right tool for your AWS projects.

---

## 2. Declarative Pipeline: The Modern Standard

Declarative is the "Formal" version of Jenkins.
It was created to solve the problem of "Spaghetti Code" in large
organizations.

### The Philosophy:
"Tell me what you want, not how to do it."
It is a **Template-based** approach.
You fill in the blanks: `agent`, `stages`, `steps`, `post`.
Jenkins handles the underlying execution logic.

### Why Seniors Love it:
1.  **Consistency:** Every Jenkinsfile in the company looks the same.
2.  **Blue Ocean:** It works perfectly with the modern Jenkins UI.
3.  **Error Checking:** Jenkins can find a typo before the build starts.
4.  **Simplicity:** It is much easier to teach to a junior developer.

---

## 3. Scripted Pipeline: The Raw Power

Scripted is the "Original" version of Jenkins.
It is basically a Groovy program that has access to the Jenkins engine.

### The Philosophy:
"I know exactly what I'm doing. Get out of my way."
It is a **Code-based** approach.
There are no templates. There are no rules.
It is just one giant `node` block filled with logic.

### Why Seniors Need it:
1.  **Dynamic Logic:** If you need to build 100 stages based on a database
    query, Declarative cannot do it.
2.  **Custom Functions:** You can define complex math and logic directly
    in the script.
3.  **Edge Cases:** If you are working with ancient legacy systems or
    extremely complex AWS networking, you need the raw power of Groovy.

---

## 4. Head-to-Head Comparison Table

| Feature | Declarative Pipeline | Scripted Pipeline |
| :--- | :--- | :--- |
| **Primary Syntax** | `pipeline { ... }` | `node { ... }` |
| **Complexity** | Low (Easy to learn). | **High (Requires Groovy).** |
| **Logic** | Limited (Directives). | **Unlimited (Raw Code).** |
| **Blue Ocean Support** | **Full / Native.** | Partial / Limited. |
| **Restart from Stage**| **Supported.** | Not supported. |
| **Safety Rails** | **Strong (Pre-check).** | None (Runtime errors). |
| **Company Standard** | **Yes (Recommended).** | No (Except for complex apps). |

---

## 5. The Senior Decision Matrix: Which one to choose?

Follow this simple logic to choose the right syntax for your next
AWS project:

- **Is it a standard Web App or Microservice?**
- **YES:** Use **Declarative**. Keep it simple and maintainable.
- **Does it follow the standard Build -> Test -> Deploy pattern?**
- **YES:** Use **Declarative**. Don't reinvent the wheel.
- **Do you need to dynamically generate stages based on a list?**
- **YES:** Use **Scripted**. (Or use a Declarative script block).
- **Are you building a massive, company-wide "Shared Library"?**
- **YES:** Use **Scripted**. You need the power of a full language.

---

## Summary

- Declarative Pipelines are the "autopilot" standard for 95% of modern AWS DevOps tasks
- Scripted Pipelines offer raw "manual control" for complex, edge-case automation scenarios
- Declarative syntax prioritizes consistency, readability, and pre-build error checking
- Scripted syntax provides unlimited logic and dynamic stage generation via raw Groovy code
- The Hybrid Path allows you to use a 'script' block inside a Declarative pipeline for specialized tasks
- Seniors choose the syntax based on the project's complexity and team maintenance requirements
- Mastering both dialects is a requirement for any top-tier Jenkins Platform Engineer

In the next Module, `Module 7: Jenkins Shared Libraries`, you will learn how to centralize your logic into professional, version-controlled automation libraries.
