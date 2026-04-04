# Invoking Build Tools: The Industrial Engines of Java

## 1. Introduction: The Chef and the Industrial Kitchen

Imagine you are a master chef who has just been hired to run the kitchen for a massive, 5-star international hotel. In your previous job at a small cafe, you did everything by hand. You chopped every onion, you whisked every egg, and you personally watched every steak on the grill. This "Hand-Crafted" approach worked for 20 people a day. But in the hotel, you have 5,000 guests arriving for breakfast at the same time. If you try to chop every onion by hand, you will fail before the first guest even sits down.

To survive in the hotel, you don't just need a bigger knife; you need **Industrial Appliances.** You need a machine that can chop 100 onions in 10 seconds. You need a specialized oven that can cook 200 eggs at an exact temperature. You need a standard, repeatable process that every junior cook can follow perfectly.

In the world of Java development, **Apache Ant and Apache Maven are those industrial appliances.**

While we *could* use a raw "Execute Shell" step to compile our code (as we learned in the previous files), doing so for a massive enterprise application is like trying to build a skyscraper with a hand-held screwdriver. You would have to manually list every single one of the 1,000 Java files in your project. You would have to manually find, download, and manage the "Class Path" for every single external library your code uses (like the AWS SDK or the Spring Framework). It would be a nightmare of complexity and human error.

Ant and Maven solve this by providing a standardized "Build Engine." They understand the "Lifecycle" of software—from cleaning the disk and compiling the source code to running unit tests and packaging the final product into a JAR or WAR file. Jenkins has built-in "Invokers" for these tools, allowing you to connect your CI/CD pipeline to these powerful engines with just a few clicks. In this file, we are going to go into **absolute, extreme depth** on how to configure these tools, why Maven has won the "Build War," and how to manage your project's dependencies like a senior DevOps professional on AWS.

---

## 2. Apache Ant: The Legacy of Total Control

Apache Ant was the first "Great" build tool for the Java ecosystem. Released in the early 2000s, it changed the way we thought about automation.

### The Philosophy of Ant:
Ant is "Procedural." This means you have to tell Ant exactly **how** to do everything. You provide a script (written in XML and named `build.xml`) that contains a list of "Targets."
-   Target 1: "Create a folder named `/bin`."
-   Target 2: "Run the `javac` compiler on every file in `/src` and put the output in `/bin`."
-   Target 3: "Zip up the `/bin` folder and name it `app.jar`."

### An Example of a "Senior" Ant `build.xml`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project name="TechCorp-Legacy-App" default="main" basedir=".">
    <!-- Define properties for paths to avoid hardcoding -->
    <property name="src.dir"     value="src"/>
    <property name="build.dir"   value="build"/>
    <property name="classes.dir" value="${build.dir}/classes"/>
    <property name="jar.dir"     value="${build.dir}/jar"/>

    <!-- The 'clean' target deletes old files -->
    <target name="clean">
        <echo message="Cleaning up the old build files..."/>
        <delete dir="${build.dir}"/>
    </target>

    <!-- The 'compile' target creates the necessary folders and runs the compiler -->
    <target name="compile" depends="clean">
        <mkdir dir="${classes.dir}"/>
        <javac srcdir="${src.dir}" destdir="${classes.dir}"/>
    </target>

    <!-- The 'jar' target packages the code into a single file -->
    <target name="jar" depends="compile">
        <mkdir dir="${jar.dir}"/>
        <jar destfile="${jar.dir}/${ant.project.name}.jar" basedir="${classes.dir}">
            <manifest>
                <attribute name="Main-Class" value="com.techcorp.Main"/>
            </manifest>
        </jar>
    </target>

    <!-- The 'main' target is the default entry point -->
    <target name="main" depends="jar"/>
</project>
```

### Why do we still use Ant?
Ant is still used today in many older "Legacy" applications. Because it is so flexible, it's also useful for tasks that aren't strictly "Java." If you need a script that moves files between three different servers, renames them based on the date, and then triggers a database update, Ant is often easier than a complex Bash script. However, Ant has one massive flaw: **It has no native support for libraries (Dependencies).** If your project needs the AWS SDK, you have to manually download the `.jar` file and put it in your project folder. This led to the creation of Maven.

---

## 3. Apache Maven: The Industry Standard

If Ant is a "Blank Piece of Paper" where you have to draw your own map, Maven is a "GPS" where you just type in the destination and it knows the standard route.

### The Philosophy of Maven:
Maven is "Declarative." You don't tell Maven **how** to build; you tell it **what** your project is. You provide a file called `pom.xml` (Project Object Model). Because Maven follows a "Standard Convention" (e.g., source code is always in `src/main/java`), it already knows how to compile and package your code without you writing a single line of script.

### The "Secret Sauce": Dependency Management
This is why Maven won the war. Inside your `pom.xml`, you simply list the libraries you need.
```xml
<dependencies>
    <dependency>
        <groupId>com.amazonaws</groupId>
        <artifactId>aws-java-sdk-s3</artifactId>
        <version>1.12.500</version>
    </dependency>
</dependencies>
```
When Jenkins runs Maven, Maven automatically goes to the internet (a place called **Maven Central**), downloads the AWS library, and makes it available to your code. It even downloads the libraries that *those* libraries need! This is called "Transitive Dependencies," and it is the foundation of modern software development.

---

## 4. Configuring the Maven "Invoker" in Jenkins

Jenkins doesn't come with Maven pre-installed. You must configure it in the "Toolroom."

1.  **Global Tool Configuration:** Go to **Manage Jenkins** → **Tools**.
2.  **Add Maven:** Give it a name like `MAVEN-3.9`.
3.  **Install Automatically:** Check this box. Jenkins will download the correct version from the Apache website the first time you run a job.
4.  **In the Job:** Go to your Freestyle job configuration.
5.  **Add Build Step:** Select **Invoke top-level Maven targets**.
6.  **Maven Version:** Select the `MAVEN-3.9` you just created.
7.  **Goals:** This is the most important part. You give Maven a list of "Tasks" to perform.

### Standard Maven Goals for DevOps:
-   **`clean`**: Deletes the `target/` folder so you are starting from a 100% fresh slate.
-   **`test`**: Runs all the unit tests in your project.
-   **`package`**: Compiles the code and creates the `.jar` or `.war` file.
-   **`install`**: Does everything above, and then puts the finished file into the local repository so other jobs can use it.
-   **Senior Tip:** In Jenkins, we almost always use the goal: **`clean install`**.

---

## 5. Troubleshooting Maven on AWS

### A. The "Out of Memory" (OOM) Error
Building large Java applications is extremely CPU and RAM intensive. Sometimes, Maven will crash with a `java.lang.OutOfMemoryError`.
-   **The Fix:** Click the "Advanced" button in your build step and find the **JVM Options** box. 
-   **The Code:** Type `-Xmx2g -Xms512m`. This tells the Java Virtual Machine: "You are allowed to use up to 2GB of RAM."

### B. The "Private Network" Problem
If your Jenkins server is in a private AWS VPC with no internet access, Maven will try to download libraries and fail.
-   **The Fix:** You must set up a **"Repository Mirror"** like **AWS CodeArtifact** or **JFrog Artifactory**. You then tell Maven to look at your internal AWS server instead of the public internet.

---

## Summary

-   **Ant and Maven** are the "Industrial Engines" that power Java automation.
-   **Ant** is procedural and flexible, used mostly for legacy projects.
-   **Maven** is declarative and standardized, and it is the #1 tool for modern CI/CD.
-   **Dependency Management** is Maven's greatest feature, allowing you to pull libraries from the internet automatically.
-   **Jenkins Tools** allow you to manage multiple versions of these engines for different projects.
-   **Performance:** Always ensure your AWS EC2 instance has enough RAM and that you have configured the Maven JVM options correctly.

In the next file, `04-post-build-actions.md`, we will learn what happens *after* the build is done. We will learn how to save the finished product and notify the team of the results.
