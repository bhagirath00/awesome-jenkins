# Installing Jenkins on Kubernetes (EKS)

## Introduction

Imagine you are the Head of Platform Engineering for a global company like Netflix or Amazon. You have 500 different developer teams, each pushing code 10 times a day. If you try to run all these builds on a single Linux server, the server will melt. If you try to manage 500 separate Docker containers, you will spend your entire life managing IP addresses and storage volumes.

This is why the world's largest companies run **Jenkins on Kubernetes.** 

In the AWS world, this means using **Amazon EKS (Elastic Kubernetes Service).** Kubernetes is like a "Super-Orchestrator." It doesn't just run a container; it manages its health, its scaling, its storage, and its network automatically. If the Jenkins container crashes, Kubernetes starts a new one in seconds. If the traffic spikes, Kubernetes can help scale the worker nodes.

In this file, we are going to go into **extreme depth** on how to deploy a production-grade Jenkins instance on Amazon EKS. we will talk about the architectural shift from "Static Servers" to "Dynamic Pods," how to manage storage using AWS EBS, and how to use **Helm** to deploy Jenkins with one single command.

---

## 1. The Architectural Shift: Static vs. Dynamic

To understand why we use Kubernetes, you must understand the difference in how builds are run.

### The Old Way: Static Agents
In a traditional setup, you have a Jenkins Controller and three Jenkins Agents (EC2 instances). If you have 50 builds to run, they have to wait in line because you only have three machines. Even if no builds are running, you are still paying AWS for those three machines. This is slow and expensive.

### The Kubernetes Way: Dynamic Pods
On Kubernetes, the "Agents" are not servers. They are **Pods.**
1.  A developer pushes code.
2.  The Jenkins Controller tells the Kubernetes API: "I need a build environment with Java 17 and Docker."
3.  Kubernetes spins up a temporary Pod with exactly those tools.
4.  The build runs, finishes, and the Pod is **deleted immediately.**
5.  **The Result:** You only pay for the exact seconds the build was running. You have "Infinite Scaling" — if you have 1,000 builds, Kubernetes starts 1,000 Pods.

---

## 2. Prerequisites: The AWS/EKS Toolbox

Before we start, you need a running **Amazon EKS Cluster**. You also need these tools on your local laptop:
- **kubectl:** The "Remote Control" for Kubernetes.
- **helm:** The "Package Manager" for Kubernetes. Think of Helm like `apt` or `yum`, but for Kubernetes apps.
- **AWS CLI:** Configured to talk to your AWS account.

---

## 3. Storage: The Foundation of Jenkins on EKS

Jenkins is a **Stateful** application. It stores everything (jobs, history, plugins) in the `/var/jenkins_home` directory. In Kubernetes, Pods are "Ephemeral" (temporary). If your Jenkins Pod restarts and you don't have a persistent disk, **you will lose all your data.**

On AWS, we use **Amazon EBS (gp3)** to solve this.

### Step 1: Create a StorageClass
We need to tell Kubernetes how to talk to the AWS EBS service.
```yaml
# storageclass.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: jenkins-ebs-sc
provisioner: ebs.csi.aws.com # The AWS EBS Driver
parameters:
  type: gp3 # Fast, modern SSD
  reclaimPolicy: Retain # If we delete Jenkins, keep the data!
volumeBindingMode: WaitForFirstConsumer
```

### Step 2: Create a PersistentVolumeClaim (PVC)
This is Jenkins' "Request" for a 20GB disk.
```yaml
# pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: jenkins-pvc
  namespace: jenkins
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: jenkins-ebs-sc
  resources:
    requests:
      storage: 20Gi
```

---

## 4. Deploying with Helm (The Professional Way)

While you *could* write 500 lines of manual YAML code to deploy Jenkins, senior engineers use **Helm**. The Jenkins community has already built a "Chart" (a package) that contains all the best practices for security and performance.

### Step 1: Add the Jenkins Repository
```bash
helm repo add jenkins https://charts.jenkins.io
helm repo update
```

### Step 2: Create a `values.yaml` File
This file allows you to customize the installation. We want to tell Helm to use our AWS disk and to create a Load Balancer so we can access Jenkins from the internet.

```yaml
# values.yaml
controller:
  componentName: "jenkins-controller"
  image: "jenkins/jenkins"
  tag: "lts"
  serviceType: LoadBalancer # Creates an AWS Network Load Balancer (NLB)
  
  persistence:
    enabled: true
    existingClaim: jenkins-pvc # Use the PVC we created above

  # Give Jenkins enough RAM to survive on EKS
  resources:
    requests:
      cpu: "500m"
      memory: "1024Mi"
    limits:
      cpu: "1000m"
      memory: "2048Mi"
```

### Step 3: The Final Deployment
```bash
# Create a namespace to keep Jenkins isolated
kubectl create namespace jenkins

# Install Jenkins using our custom values
helm install my-jenkins jenkins/jenkins \
  --namespace jenkins \
  -f values.yaml
```

---

## 5. Exposing Jenkins: The AWS Load Balancer

Once the Helm command finishes, AWS will automatically go into its "Engine Room" and create an **Elastic Load Balancer (ELB).**

### How to get your URL:
```bash
kubectl get svc -n jenkins
```
Look for the `EXTERNAL-IP`. It will be a long address like `a1b2c3d4e5-us-east-1.elb.amazonaws.com`. Paste this into your browser, and you are live!

---

## 6. Senior Secret: IAM Roles for Service Accounts (IRSA)

In a professional AWS setup, your Jenkins Pod will need to talk to other AWS services (like S3 or ECR). 

**The Junior Way:** Copy-paste AWS Access Keys into the Jenkins UI. This is **UNSAFE**. If someone hacks your Jenkins, they have your root keys.

**The Senior Way:** Use **IRSA**.
1.  Create an IAM Role in AWS with permissions (e.g., `S3FullAccess`).
2.  Tell Kubernetes: "This Jenkins Pod is allowed to use this IAM Role."
3.  The Jenkins Pod now has permission to talk to AWS **without having any passwords or keys stored inside it.** This is the highest level of security possible.

---

## 7. Troubleshooting Jenkins on Kubernetes

### A. The "Pending" Pod
If your Jenkins Pod is stuck in "Pending" status, it usually means Kubernetes couldn't find a disk for it.
- **The Fix:** Run `kubectl describe pod -n jenkins` and check the events at the bottom. It will likely say "Failed to attach volume." Ensure your EBS CSI Driver is installed on the cluster.

### B. OOMKilled (Out of Memory)
If the Pod starts and then disappears, Kubernetes might have killed it for using too much RAM.
- **The Fix:** Increase the `memory` limit in your `values.yaml` file and run `helm upgrade`.

---

## Summary

-   **Kubernetes (EKS)** is the ultimate platform for high-scale Jenkins deployments.
-   **Dynamic Agents** allow you to scale your build power to infinity while only paying for what you use.
-   **Persistent Storage** is non-negotiable. Use AWS EBS (gp3) to keep your data safe.
-   **Helm** is the industry standard for managing Kubernetes applications like Jenkins.
-   **IRSA** is the professional way to handle AWS permissions without using secret keys.

In the next file, `04-install-windows.md`, we will look at a more specialized use case: installing Jenkins on Windows for .NET and game development workloads.
