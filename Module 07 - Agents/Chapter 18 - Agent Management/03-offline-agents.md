# Offline Agents: Handling the Silence

## 1. Introduction: The Ghost Ship

Imagine you are a **Radio Operator** on a busy coast.
Every day, you maintain contact with hundreds of ships at sea.
The airwaves are filled with the sound of "Status Reports," "Weather
Updates," and "General Chatter."
The constant noise is a sign of a healthy, working fleet.
But then, suddenly, one of your regular ships goes **Silent**.

You call out to the ship on every channel, but there is no answer.
There is only the "Static" of the empty airwaves.
This is a **Ghost Ship**.
You don't just ignore the silence and go to lunch.
Silence is a "Signal" of its own.
It could mean the ship has run out of fuel. It could mean the radio
equipment is broken. Or it could mean the ship has been hijacked
or has sunk.

In the world of Jenkins and AWS, an **Offline Agent** is that ghost
ship.
When an agent disappears from the Jenkins dashboard, it is a critical
event that requires immediate investigation.
If you have 10 jobs waiting to run, but your "Linux" agents are
offline, your entire company's productivity will grind to a halt.

## 2. Why do Agents go Offline?

A senior DevOps engineer knows the "Top 3" reasons for the silence.

### Reason 1: The "Disk Space" Crash
As we learned in the previous file, if the disk fills up to 100%,
the Jenkins agent software (the `agent.jar`) will crash.
The server is still "On," but it can no longer talk to the controller.

### Reason 2: The "Network Partition"
AWS is incredibly reliable, but sometimes a network cable fails or a
routing table is updated incorrectly.
The controller is in one "Room," and the agent is in another "Room,"
and the door between them has been locked.

### Reason 3: The "Credential Expired"
If you are using SSH keys to connect, and that key has been rotated or
deleted from the AWS IAM console, the controller will be "Kicked Out"
the next time it tries to log in.

---

## 3. The Recovery Protocol

When the silence hits, a master builder follows a **Recovery Protocol**.

1.  **Check the Agent Logs:** The controller keeps a "Connection Log"
    for every node. This is the first place you look. It will tell
    you exactly what the last "Sound" from the agent was.
2.  **Ping the Server:** Can you still reach the AWS EC2 instance using
    your terminal? If "Yes," the problem is the Jenkins software.
    If "No," the problem is the server itself.
3.  **The "Relaunch" Button:** Jenkins has a built-in button to "Launch
    Agent." Sometimes, a simple "Reboot" of the connection is all it
    takes to bring the ghost ship back to life.

---

## Summary

- An "offline" status is a critical signal that an agent has lost contact with the controller
- Common causes of agent silence include disk exhaustion, network partitions, and expired keys
- The Jenkins agent connection log is the primary tool for diagnosing the root cause of failure
- A "ghost ship" agent can halt the entire company's pipeline queue if not recovered quickly
- Checking server-level connectivity via ping or SSH is the first step in the recovery protocol
- The "Launch Agent" button can often resolve minor software crashes or connection timeouts
- Proactive monitoring (from Chapter 18.2) is the best way to prevent agents from going silent

In the next file, `04-agent-best-practices.md`, you will learn the senior-level rules for building a world-class, never-silent worker fleet.
