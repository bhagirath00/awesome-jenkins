# Managing Plugins: The Curator's Duty

## 1. Introduction: The Museum Curator

Imagine you are the **Chief Curator** of a world-famous museum.
The museum has a massive collection of 100,000 artifacts from all
over history.
But you can't just put all 100,000 items in the display cases at
the same time.
If you did, the museum would be crowded, the visitors would be
confused, and the floor might collapse from the weight.

Instead, you must be a **Curator**.
You must choose the "Best" items for the display.
You must ensure that the items are cleaned and restored (Updated).
You must ensure that the items are authentic and safe (Security).
And you must put away or "Archive" the items that are no longer
relevant to the current exhibition.
Your job is not just to "Have" things; it is to **Manage** the collection
with precision and care.

In the world of Jenkins, **Managing Plugins** is that curator's duty.
It is the process of keeping your automation platform lean, secure,
and up-to-date.
A senior DevOps engineer knows that a "Forgotten Plugin" is a security
hole waiting to be exploited.

## 2. The Maintenance Cycle

A professional Jenkins environment follows a "Maintenance Rhythm."

-   **Audit (Monthly):** Go through your list of installed plugins.
    If you see a plugin that hasn't been used in 3 months, **Delete it**.
    Every extra line of code is a potential point of failure.
-   **Update (Bi-Weekly):** Check for security patches. If a plugin has a
    "Vulnerability Alert" (Red Shield icon), you must update it
    immediately.
-   **Pinning:** Sometimes, a new version of a plugin is broken.
    A senior engineer knows how to "Pin" a specific version to prevent
    automatic updates from breaking the production pipeline.

---

## 3. Handling Plugin Failures

Sometimes, a plugin update goes wrong.
The "Display Case" breaks, and the museum goes into a crisis.

1.  **The Backup:** Never update plugins without a full backup of
    your `JENKINS_HOME` directory.
2.  **The Rollback:** If an update fails, you should be able to restore
    the old `.hpi` file from your backup and restart Jenkins.
3.  **The Safe Mode:** If Jenkins won't start at all because of a bad
    plugin, you can manually delete the plugin folder from the AWS
    EC2 terminal to "Force" the system back into a working state.

---

## Summary

- Managing plugins is a continuous duty of auditing, updating, and securing the platform
- Regular audits to remove unused plugins reduce the "attack surface" and improve stability
- Security patches for plugins must be prioritized to protect the Jenkins controller from exploits
- Pinning versions is a senior technique used to prevent unstable updates from breaking pipelines
- Always maintain a full backup of JENKINS_HOME before performing any plugin modifications
- Manual rollback of .hpi files is the primary recovery method for failed plugin updates
- A lean and curated plugin collection ensures a high-performance automation engine

In the next file, `04-plugin-compatibility.md`, you will learn how to handle the complex relationships and "dependency hell" of the plugin ecosystem.
