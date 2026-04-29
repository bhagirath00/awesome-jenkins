# Installing Plugins: Expanding the Horizon

## 1. Introduction: The Universal Toolbelt

Imagine you are a **Master Repair Person**.
You have a high-quality leather toolbelt.
When you first start your career, your toolbelt has the basics:
a hammer, a screwdriver, and a pair of pliers.
These tools are great for simple household repairs.
But as you become more successful, you start taking on bigger and
more complex jobs.

One day, you are asked to install a complex solar energy system.
The hammer won't help you there.
You need a specialized **Voltmeter**.
So, you go to the tool store, you find the best voltmeter on the
shelf, and you slide it into a spare loop on your belt.
Now, you are more than just a repair person—you are a solar technician.
The toolbelt (the **Jenkins Controller**) hasn't changed, but your
capability (the **Plugins**) has expanded.

In the world of Jenkins, **Installing a Plugin** is adding a new
tool to your belt.
It is the process of finding, downloading, and activating a new
capability so that you can solve the next big DevOps challenge on AWS.

## 2. The Plugin Manager

The "Manage Plugins" page is your tool store.
It is divided into four main sections:

1.  **Updates:** Shows you which of your existing tools have a new,
    better version available. (A senior rule: Always read the "Changelog"
    before clicking update!)
2.  **Available:** The massive catalog of 1,800+ tools you can download.
3.  **Installed:** The tools currently on your belt. This is where you
    go to "Clean Up" and delete tools you no longer use.
4.  **Advanced:** For manual installation of "Custom" tools that aren't
    in the main store.

---

## 3. The Installation Workflow

A professional plugin installation follows a safe and predictable path.

-   **Search:** Find the plugin by name (e.g., "Amazon EC2" or "Slack").
-   **Dependencies:** Jenkins automatically checks if your new tool
    needs *other* tools to work. It downloads them all together as a
    "Set."
-   **The Restart Choice:** Some tools can be added while you work.
    Others require you to "Restart" the Jenkins controller.
-   **The Senior Rule:** Never restart Jenkins in the middle of a
    busy workday. Always schedule your "Tool Upgrades" during a
    maintenance window when no builds are running.

---

## Summary

- The Plugin Manager is the central hub for finding and installing new Jenkins capabilities
- The "Available" tab provides a searchable catalog of over 1,800 community plugins
- Jenkins automatically manages plugin dependencies, ensuring all required components are installed
- Some plugins require a controller restart to activate, which should be scheduled carefully
- The "Installed" tab allows engineers to audit and remove unnecessary or outdated tools
- Senior engineers always review the changelog before updating critical plugins to avoid regressions
- Manual plugin installation via the "Advanced" tab is reserved for custom or private integrations

In the next file, `03-managing-plugins.md`, you will learn how to maintain and secure your collection of tools over time.
