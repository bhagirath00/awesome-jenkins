# Deploy to Kubernetes: The Cluster Orchestrator

## 1. Introduction: The Puppet Master and the Marionettes

Imagine you are a **Master Puppet Master** performing a grand opera.
You have dozens of puppets on the stage.
Some puppets are characters, some are animals, and some are even
moving scenery.
Each puppet is a complex machine with hundreds of strings.

You can't possibly pull every string yourself. That would be a
"Manual Disaster."
Instead, you use a **Universal Control Bar**.
When you move the bar, the system automatically pulls the correct
strings on every puppet simultaneously.
If you want the puppets to "Dance," you just send one command, and
the system coordinates the movement of every leg and every arm.

In the world of DevOps, **Kubernetes (K8s)** is that universal control
bar.
The "Puppets" are your **Docker Containers**.
The "Strings" are the **Kubernetes Manifests (YAML files)**.
Deploying to Kubernetes with Jenkins is the process of the puppet
master (Jenkins) telling the control bar (the K8s API) to "Change
 the Performance."

## 2. The `kubernetesDeploy` Step

A senior DevOps engineer uses the **Kubernetes Continuous Deploy**
plugin to manage the orchestration.

```groovy
stage('Deploy to K8s') {
    steps {
        // We are telling the Puppet Master to update the stage
        kubernetesDeploy(
            configs: 'k8s/deployment.yaml',
            kubeconfigId: 'my-eks-cluster-config'
        )
    }
}
```

### Why use this plugin?
-   **Declarative Updates:** You don't tell Kubernetes "Delete the old
    container and start a new one." Instead, you just show it the
    "New Blueprint" (the YAML file), and Kubernetes figures out the
    fastest and safest way to make the change.
-   **Zero-Downtime:** Kubernetes performs a **Rolling Update**. It
    replaces one container at a time, ensuring that your app is
    always available to your customers during the deployment.

---

## 3. The Senior Strategy: Helm Charts

A master builder doesn't just use raw YAML files. They use **Helm**.
Helm is a "Package Manager" for Kubernetes.
It allows you to use "Variables" in your deployment.
-   `image: my-app:${env.BUILD_ID}`
-   `replicas: ${env.TARGET_REPLICAS}`
Jenkins "Fills in the Blanks" in your Helm chart and "Pushes" the
result to the cluster. This is the ultimate in **Customizable
Automation**.

---

## Summary

- Kubernetes acts as a universal "control bar" for orchestrating a fleet of containerized apps
- Deploying to K8s with Jenkins involves sending updated "blueprints" (YAML) to the cluster API
- The kubernetesDeploy step ensures that updates are handled in a safe, declarative manner
- Rolling updates provide zero-downtime deployments by replacing containers one by one
- Helm charts allow for customizable automation by injecting build-specific variables into manifests
- Using Kubeconfig credentials ensures a secure and authorized connection to the AWS EKS cluster
- Mastering Kubernetes orchestration is the hallmark of a senior-level Platform Engineer

In the next file, `03-ansible-integration.md`, you will learn how to manage legacy and non-containerized servers.
