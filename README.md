<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://cdn.simpleicons.org/jenkins/white" />
    <img alt="Jenkins Logo" src="https://cdn.simpleicons.org/jenkins/black" width="60" />
  </picture>
</p>

<h1 align="center">Awesome Jenkins</h1>

<p align="center">
  <a href="https://awesome.re"><picture><source media="(prefers-color-scheme: dark)" srcset="https://shieldcn.dev/badge/awesome.svg?variant=outline&amp;mode=dark&amp;font=geist&amp;logo=awesomelists" /><img alt="awesome" src="https://shieldcn.dev/badge/awesome.svg?variant=outline&amp;mode=light&amp;font=geist&amp;logo=awesomelists" /></picture></a>
  <a href="https://www.jenkins.io/"><picture><source media="(prefers-color-scheme: dark)" srcset="https://shieldcn.dev/badge/Jenkins-2.400+.svg?variant=outline&amp;mode=dark&amp;font=geist&amp;logo=jenkins" /><img alt="Jenkins" src="https://shieldcn.dev/badge/Jenkins-2.400+.svg?variant=outline&amp;mode=light&amp;font=geist&amp;logo=jenkins" /></picture></a>
  <a href="LICENSE"><picture><source media="(prefers-color-scheme: dark)" srcset="https://shieldcn.dev/badge/license-MIT.svg?variant=outline&amp;mode=dark&amp;font=geist" /><img alt="license" src="https://shieldcn.dev/badge/license-MIT.svg?variant=outline&amp;mode=light&amp;font=geist" /></picture></a>
  <a href="https://github.com/bhagirath00/awesome-jenkins"><picture><source media="(prefers-color-scheme: dark)" srcset="https://shieldcn.dev/views/repo/bhagirath00/awesome-jenkins.svg?variant=outline&amp;mode=dark&amp;font=geist" /><img alt="views" src="https://shieldcn.dev/views/repo/bhagirath00/awesome-jenkins.svg?variant=outline&amp;mode=light&amp;font=geist" /></picture></a>
</p>

<p align="center">
  <em>A curated, professional-grade guide to mastering Jenkins CI/CD automation, from basic freestyle builds to complex declarative pipelines and cloud-native Kubernetes integrations.</em>
</p>

---

## Table of Contents

- [Introduction](#introduction)
- [Why Jenkins for DevOps?](#why-jenkins-for-devops)
- [Learning Modules](#learning-modules)
  - [Phase 1: Core Fundamentals & Installation](#phase-1-core-fundamentals--installation)
  - [Phase 2: Pipeline Engineering (Declarative & Scripted)](#phase-2-pipeline-engineering-declarative--scripted)
  - [Phase 3: Distributed Architecture & Plugins](#phase-3-distributed-architecture--plugins)
  - [Phase 4: Security Hardening & Enterprise Integrations](#phase-4-security-hardening--enterprise-integrations)
- [Resources](#resources)
- [Contributing](#contributing)
- [Security](#security)
- [License](#license)

---

## Introduction

Welcome to **Awesome Jenkins**! This repository serves as an exhaustive, hands-on architectural roadmap and reference manual designed for site reliability engineers (SREs), DevOps practitioners, and platform engineers automating continuous integration and continuous delivery.

> **"If you have to do something more than once, automate it."** — The core ethos of CI/CD engineering.

---

## Why Jenkins for DevOps?

Jenkins remains the world's most battle-tested open-source automation server:

| Capability | Importance in DevOps |
| :--- | :--- |
| **Pipeline as Code** | Version-controlled, reproducible pipelines defined in declarative or scripted `Jenkinsfile`s |
| **Massive Ecosystem** | 1,800+ community plugins integrating with every tool in the modern cloud-native stack |
| **Distributed Build Agents** | Horizontal scalability with static VMs, SSH nodes, and dynamic ephemeral Kubernetes pods |
| **Self-Hosted Sovereignty** | Complete control over your CI/CD execution environment, compliance boundaries, and secrets |

---

## Learning Modules

Organized into 10 structured modules covering 28 chapters and 111 production-grade guides.

### Phase 1: Core Fundamentals & Installation

| Module | Description |
| :--- | :--- |
| [**Module 01 - Introduction**](./Module%2001%20-%20Introduction/) | What is Jenkins, Controller-Agent architecture, CI/CD lifecycles, and tool comparisons |
| [**Module 02 - Installation**](./Module%2002%20-%20Installation/) | Deploying Jenkins on Linux, Docker, Helm on Kubernetes, initial unlock, and admin setup |
| [**Module 03 - Freestyle Jobs**](./Module%2003%20-%20Freestyle%20Jobs/) | Legacy freestyle jobs, build triggers, source code management (Git), post-build actions |

### Phase 2: Pipeline Engineering (Declarative & Scripted)

| Module | Description |
| :--- | :--- |
| [**Module 04 - Pipeline Basics**](./Module%2004%20-%20Pipeline%20Basics/) | Pipeline concepts, stages, steps, polling vs webhooks, and multi-branch pipeline projects |
| [**Module 05 - Declarative Pipelines**](./Module%2005%20-%20Declarative%20Pipelines/) | Declarative syntax: `agent`, `environment`, `parameters`, `stages`, `parallel`, `when`, and `post` |
| [**Module 06 - Scripted Pipelines**](./Module%2006%20-%20Scripted%20Pipelines/) | Groovy-based scripted pipelines, control flow, `node` blocks, custom functions, and shared libraries |

### Phase 3: Distributed Architecture & Plugins

| Module | Description |
| :--- | :--- |
| [**Module 07 - Agents**](./Module%2007%20-%20Agents/) | Permanent agents via SSH/JNLP, ephemeral Docker agents, and dynamic Kubernetes pod agents |
| [**Module 08 - Plugins**](./Module%2008%20-%20Plugins/) | Plugin management, plugin installation strategies, essential DevOps plugins, and notifications |

### Phase 4: Security Hardening & Enterprise Integrations

| Module | Description |
| :--- | :--- |
| [**Module 09 - Security**](./Module%2009%20-%20Security/) | Role-Based Access Control (Matrix/Role Strategy), Credentials Provider, and Script Approval security |
| [**Module 10 - Integrations**](./Module%2010%20-%20Integrations/) | Real-world integrations: GitHub, GitLab, Docker, Kubernetes, AWS, Terraform, and Prometheus |

---

## Resources

### Official Documentation

| Resource | Link |
| :--- | :--- |
| Jenkins Documentation | [jenkins.io/doc](https://www.jenkins.io/doc/) |
| Pipeline Syntax Reference | [jenkins.io/doc/book/pipeline/syntax](https://www.jenkins.io/doc/book/pipeline/syntax/) |
| Jenkins Plugins Index | [plugins.jenkins.io](https://plugins.jenkins.io/) |
| Jenkins Security Advisories | [jenkins.io/security](https://www.jenkins.io/security/) |

### Recommended Tools

- **Jenkins Configuration as Code (JCasC)**: Declarative automation for Jenkins server configuration.
- **Job DSL Plugin**: Programmatic job generation using Groovy DSLs.
- **Hadolint / Trivy**: Security scanners integrated inside pipeline stages.
- **Kubernetes CLI Plugin**: Native dynamic pod provisioning for scalable build agents.

---

## Contributing

Contributions are welcome! Please read our [Contributing Guidelines](CONTRIBUTING.md) before submitting a Pull Request.

---

## Security

Please review our [Security Policy](SECURITY.md) for credential binding rules, Groovy sandbox safety, and vulnerability reporting.

---

## License

This educational content is open source and available under the **MIT License**. See [LICENSE](LICENSE) for details.

<p align="center">
  <strong>Happy Automating!</strong>
</p>
