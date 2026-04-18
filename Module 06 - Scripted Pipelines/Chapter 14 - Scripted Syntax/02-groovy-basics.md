# Groovy Basics for Jenkins: The Language of the Machine

## 1. Introduction: The Universal Translator

Imagine you are a world-class diplomat who has been assigned to work
at the United Nations.
The United Nations is a place where people from 193 different countries
come together to solve the world's most complex problems.
Every country has its own language, its own culture, and its own way
of doing things.
To be successful as a diplomat, you don't need to speak every single
one of those 193 languages perfectly.
Instead, you need a **Universal Translator**.

You need a language that is flexible enough to express complex ideas,
simple enough for everyone to understand, and powerful enough to
bridge the gap between different cultures.
In the world of Java and the JVM (Java Virtual Machine), **Groovy** is
that universal translator.
It is a language that was designed to combine the power of Java with
the speed and flexibility of a scripting language like Python.
It is the "Native Tongue" of the Jenkins Pipeline engine.

In this file, we are going to go into **absolute, exhaustive technical depth**
on the basics of the Groovy language.
We will look at how to define variables, how to manipulate strings,
and the "Magic" of the Jenkins API.
A senior DevOps engineer knows that you don't need to be a professional
software developer to use Groovy.
But you *do* need to understand the fundamental grammar of the language
if you want to build advanced, self-healing pipelines that can manage
a massive AWS cloud infrastructure with total precision.

---

## 2. Variables and Data Types: The Storage Boxes

In Groovy, everything is an object.
However, we have two different ways to define our "Storage Boxes"
for data.

### The `def` Keyword (Dynamic Typing)
This is the most common way to define a variable in a Jenkins script.
`def` stands for "Define."
When you use `def`, you are telling Groovy: "I want a box, but I'm
not going to tell you yet what is going inside it. Figure it out."
- **Example:** `def myName = "Jenkins"`
- **The Senior Benefit:** It is fast and flexible. You can change the
  type of the variable later in the script without the machine
  complaining.

### Strong Typing (Java Style)
Because Groovy is built on top of Java, you can also be very specific.
- **Example:** `String myName = "Jenkins"`
- **Example:** `Integer myAge = 10`
- **The Senior Benefit:** It makes your code safer. If someone tries
  to put a "Number" into a "String" box, the script will immediately
  throw an error. This is great for preventing bugs in critical
  deployment scripts.

---

## 3. The Magic of Strings: Quotes and Interpolation

Groovy has the most powerful string handling of any language on the JVM.
As a DevOps engineer, you will spend 90% of your time working with
strings (Git URLs, AWS tags, folder paths).

### Single Quotes ('...')
Single quotes are for "Static" strings.
What you type is exactly what you get.
- **Example:** `echo 'Hello ${name}'`
- **Result:** It will literally print `Hello ${name}` on the screen.
- **Use Case:** Use single quotes for 90% of your commands to avoid
  accidental variable injection.

### Double Quotes ("...")
Double quotes are for **Interpolated** strings.
This means Groovy will look inside the string for a `${}` symbol and
replace it with the value of a variable.
- **Example:** `def name = "Sarah"`
- **Example:** `echo "Hello ${name}"`
- **Result:** It will print `Hello Sarah`.
- **Use Case:** Use this when you need to build dynamic paths or
  AWS tags like `"myapp-version-${env.BUILD_NUMBER}"`.

---

## 4. Collections: Lists and Maps

In a senior-level pipeline, you often have to manage "Groups" of things.
Groovy makes this incredibly easy.

### Lists (The Ordered Row)
A list is a sequence of items.
- **Syntax:** `def servers = ['web1', 'web2', 'web3']`
- **Access:** `echo servers[0]` (Prints `web1`).

### Maps (The Key-Value Dictionary)
A map is a collection of "Labelled" items.
This is the perfect way to manage AWS configuration.
- **Syntax:** `def config = [region: 'us-east-1', size: 't3.micro']`
- **Access:** `echo config.region` (Prints `us-east-1`).

---

## 5. Summary Table: The Groovy Basics

| Feature | Syntax | Senior Use Case |
| :--- | :--- | :--- |
| **Variable** | `def x = 1` | Managing temporary build data. |
| **Static String**| `'...'` | Constant shell commands. |
| **Dynamic String**| `"..."` | Building AWS S3 paths with variables. |
| **List** | `[...]` | Iterating through a list of AWS regions. |
| **Map** | `[k: v]` | Storing environment-specific config. |

---

## Summary

- Groovy is the primary scripting language that powers the Jenkins Pipeline engine
- The 'def' keyword allows for dynamic variable definition and flexible data handling
- Strong typing can be used for critical scripts to improve safety and catch errors early
- Single quotes define static strings, while double quotes enable variable interpolation
- Collections like Lists and Maps are essential for managing groups of AWS resources or configurations
- Groovy's close relationship with Java allows it to handle enterprise-scale automation tasks
- Mastering basic Groovy syntax is a prerequisite for writing professional Scripted Pipelines

In the next file, `03-node-and-stage.md`, you will learn how to apply these Groovy basics to the structural blocks of a Scripted pipeline.
