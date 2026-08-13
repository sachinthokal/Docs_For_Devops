# GitOps Complete Guide: Zero to Hero 🚀

Welcome to the ultimate guide on **GitOps**. This guide covers core GitOps principles, declarative continuous delivery, ArgoCD vs FluxCD, and Git-driven cluster synchronization.

---

## 📑 Table of Contents
1. [What is GitOps?](#what-is-gitops)
2. [Core Principles of GitOps](#core-principles-of-gitops)
3. [Push vs. Pull Continuous Delivery](#push-vs-pull-continuous-delivery)
4. [Key Benefits of GitOps](#key-benefits-of-gitops)
5. [Module Directory](#module-directory)

---

## What is GitOps?

**GitOps** is an operational framework that takes DevOps best practices used for application development—such as version control, code review, and automated CI/CD—and applies them to infrastructure and application deployment.

> **Key Definition**: In GitOps, **Git is the single source of truth** for your declarative infrastructure and application configurations. Any change to cluster state must be made via Git commits.

---

## Core Principles of GitOps

1. **Declarative State**: The entire system configuration is defined declaratively (e.g., Kubernetes YAMLs, Helm charts).
2. **Version Controlled & Immutable**: State definitions are stored in Git and versioned with full audit trails.
3. **Automated Pull & Sync**: Software agents automatically pull desired state changes from Git into target clusters.
4. **Self-Healing & Drift Reconciliation**: Agents continuously monitor live cluster state and reconcile differences back to Git desired state.

---

## Push vs. Pull Continuous Delivery

| Feature | Traditional Push CD (e.g., Jenkins) | GitOps Pull CD (e.g., ArgoCD) |
| :--- | :--- | :--- |
| **Trigger** | CI server pushes manifests to cluster. | In-cluster operator pulls changes from Git. |
| **Cluster Access** | CI server requires direct admin access/credentials. | Cluster pulls internally; no external admin ports exposed. |
| **Drift Detection**| None natively (out-of-band cluster edits persist).| Automatic continuous drift detection and auto-sync. |

---

## Key Benefits of GitOps

- **Enhanced Security**: Zero external cluster credentials exposed in CI pipelines.
- **Auditability & Traceability**: Clear Git commit history for every cluster change (`git log`, `git revert`).
- **Faster Recovery**: Roll back entire cluster outages in seconds via `git revert`.

---

## Module Directory

- 🏛️ **[GitOps Architecture](architecture.md)** — ArgoCD controller architecture, reconciliation loops, and ApplicationSet mechanics.
- ⚙️ **[GitOps Installations](installation.md)** — Installing ArgoCD and FluxCD operators on Kubernetes.
- ⚡ **[GitOps Commands](commands.md)** — ArgoCD CLI (`argocd`) and Flux CLI (`flux`) complete command reference.