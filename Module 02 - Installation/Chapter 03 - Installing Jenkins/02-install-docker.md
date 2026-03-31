# Installing Jenkins with Docker

## Introduction

Imagine you are moving to a new house. You have two options: You can pack every single dish, book, and piece of clothing into your car, drive it over, and unpack it one by one. Or, you could hire a professional moving company that puts everything into a single, standardized **Shipping Container.** When the container arrives at the new house, they just drop it on the lawn, and everything inside — your kitchen, your library, your bedroom — is exactly where you left it.

In the world of DevOps, **Docker is that shipping container.** 

Running Jenkins as a "normal" application on Linux (as we did in the last file) is fine, but it can be messy. You have to manage Java versions, dependencies, and file paths. If you want to move your Jenkins server to a different AWS region, you have to do all that work again from scratch.

By running **Jenkins in Docker**, we package the entire Jenkins environment — the Java version, the web server, the configuration, and the plugins — into one single image. You can run this image on your laptop, on an EC2 instance, or even in a massive Kubernetes cluster, and it will work exactly the same way every time.

In this file, we will go into **extreme depth** on how to run Jenkins as a container. We will talk about **Persistent Storage** (so you don't lose your data when the container stops), **Docker Networking**, and the "Holy Grail" of DevOps: **Docker-in-Docker** (letting Jenkins build Docker images inside its own container).

---

## 1. Why Run Jenkins in Docker? (The Senior Perspective)

Before we look at the commands, you need to understand why a senior engineer would choose Docker over a native Linux installation:

### A. Total Isolation
Your Jenkins server often needs strange dependencies. Maybe it needs an old version of Python or a specific library. If you install these on the main Linux server, you might break other tools. With Docker, the dependencies are trapped inside the container. The main server stays clean.

### B. "Instant" Upgrades and Rollbacks
If you want to upgrade Jenkins from version 2.401 to 2.440, you don't run a complex update script. You simply stop the container, change the "Tag" in your Docker command, and start it again. If the new version breaks your plugins, you just change the tag back and restart. The whole process takes 10 seconds.

### C. Identical Environments
A junior engineer says, "It worked on my machine!" A senior engineer says, "It worked in the Docker container, so it will work in production." Docker ensures that the environment on your local laptop is 100% identical to the environment in AWS.

---

## 2. Choosing the Right Jenkins Image

If you search for "Jenkins" on Docker Hub, you will see hundreds of versions. Picking the wrong one can lead to security vulnerabilities or broken plugins.

| Image Name | Recommendation | Use Case |
| :--- | :--- | :--- |
| `jenkins/jenkins:lts` | **BEST** | Long-Term Support. Most stable. Use this for production. |
| `jenkins/jenkins:latest` | **AVOID** | The cutting-edge version. May have bugs or broken plugins. |
| `jenkins/jenkins:jdk17` | **GOOD** | Specific version using Java 17. Use if you need a specific JDK. |
| `jenkins/jenkins:alpine` | **ADVANCED** | Ultra-small image. Fast to download but harder to troubleshoot. |

---

## 3. The Lifecycle of Data: Persistent Volumes

This is the most critical part of running Jenkins in Docker. 

**The Rule:** A Docker container is **Ephemeral**. This means that any file created inside a container is deleted the moment the container stops. If you create 100 Jenkins jobs and then restart the container, **all your jobs will be gone.**

To fix this, we use **Docker Volumes**. A volume is a "Bridge" between the container and your Linux server's hard drive.

### The Professional Run Command:
```bash
# 1. Create a named volume to store your data safely
docker volume create jenkins_data

# 2. Run the container with the volume mapped
docker run -d \
  --name jenkins-master \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_data:/var/jenkins_home \
  --restart=on-failure \
  jenkins/jenkins:lts
```

**What did we just do?**
- `-d`: Runs the container in the background (Detached).
- `--name`: Gives the container a friendly name so we don't have to use long IDs.
- `-p 8080:8080`: Maps the web interface to the server's port.
- `-v jenkins_data:/var/jenkins_home`: This maps the `/var/jenkins_home` directory inside the container to the `jenkins_data` volume on your AWS server. Now, your jobs and history are safe forever.
- `--restart=on-failure`: If the server reboots or Docker crashes, it will automatically restart Jenkins.

---

## 4. The "Docker-in-Docker" (DinD) Challenge

In a modern DevOps pipeline, you want Jenkins to build Docker images for your applications. But wait... Jenkins is *already* running inside Docker! How can a container run a Docker command?

### The "Socket Mounting" Solution:
Instead of trying to install Docker *inside* the Jenkins container (which is complex and slow), we simply "Share" the host's Docker engine with the container.

```bash
docker run -d \
  --name jenkins-master \
  -u root \
  -p 8080:8080 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v $(which docker):/usr/bin/docker \
  -v jenkins_data:/var/jenkins_home \
  jenkins/jenkins:lts
```

**Why `-v /var/run/docker.sock:/var/run/docker.sock`?**
The `.sock` file is the "Brain" of Docker on your Linux server. By sharing this file with the Jenkins container, you are giving Jenkins the power to control the host's Docker engine. Now, Jenkins can run `docker build` and `docker push` as if it were installed directly on the server.

---

## 5. Using Docker Compose (The Clean Way)

Senior engineers hate typing long `docker run` commands. They use **Docker Compose**. This allows you to define your entire Jenkins environment in a single YAML file that can be checked into Git.

### `docker-compose.yml` for Jenkins:
```yaml
version: '3.8'
services:
  jenkins:
    image: jenkins/jenkins:lts
    container_name: jenkins-master
    user: root # Needed for Docker-in-Docker permissions
    ports:
      - "8080:8080"
      - "50000:50000"
    volumes:
      - jenkins_data:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
    restart: always

volumes:
  jenkins_data:
```

**How to run it:**
```bash
# Start Jenkins
docker-compose up -d

# Check the logs to find the initial admin password
docker-compose logs -f
```

---

## 6. Troubleshooting Docker Jenkins

### A. Permission Denied Errors
The user inside the Jenkins container (UID 1000) might not have permission to write to your Linux folder. 
- **The Fix:** `sudo chown -R 1000:1000 /var/lib/docker/volumes/jenkins_data`

### B. Finding the Initial Password
Since you can't easily "cat" a file inside a container, use the `exec` command:
```bash
docker exec jenkins-master cat /var/jenkins_home/secrets/initialAdminPassword
```

### C. Port Conflicts
If you already have another web server running on port 8080, Jenkins will fail to start.
- **The Fix:** Change the mapping to `-p 9090:8080`. Now you can access Jenkins at `http://your-ip:9090`.

---

## Summary

-   **Docker** provides a portable, isolated, and standardized home for Jenkins.
-   **Persistent Volumes** are mandatory. Without them, you will lose your data every time you restart.
-   **Socket Mounting** allows Jenkins to build Docker images of your apps without complex "Docker-in-Docker" setups.
-   **Docker Compose** is the best practice for managing your Jenkins configuration as code.
-   **Upgrades** become trivial: change the image tag and restart.

In the next file, `03-install-kubernetes.md`, we will take automation to the next level by running Jenkins in an **AWS EKS (Kubernetes)** cluster for massive scale.
