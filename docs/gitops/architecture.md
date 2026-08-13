# GitOps Architecture & Internal Reconciliation Loops 🏛️

Detailed breakdown of GitOps engine internals, reconciliation loops, and ArgoCD architectural components.

---

## 1. ArgoCD High-Level Architecture

```
+-----------------------------------------------------------------------------------+
|                                 GIT REPOSITORY                                    |
|                      (Single Source of Truth - Kubernetes YAMLs)                  |
+----------------------------------------+------------------------------------------+
                                         |
                                         | Read Desired State
                                         v
+-----------------------------------------------------------------------------------+
|                            ARGOCD CONTROL PLANE                                   |
|                                                                                   |
|  +--------------------+    +--------------------+    +-------------------------+  |
|  |   API Server       |    |  Repo Server       |    | Application Controller  |  |
|  | (UI & CLI Target)  |    | (Fetches Git Repos)|    | (Reconciliation Engine) |  |
|  +--------------------+    +--------------------+    +------------+------------+  |
+-------------------------------------------------------------------|---------------+
                                                                    |
                                                                    | Reconciles State
                                                                    v
+-----------------------------------------------------------------------------------+
|                            TARGET KUBERNETES CLUSTER                              |
|                                                                                   |
|  - Live Resources (Pods, Deployments, Services, Ingress)                          |
|  - Continuous Drift Detection Loop (Target State vs Desired State)                |
+-----------------------------------------------------------------------------------+
```

---

## 2. The Continuous Reconciliation Loop

```
  +-----------------------+           Compare           +-----------------------+
  |  Git Desired State    | <-------------------------> |  Live Cluster State   |
  +-----------+-----------+                             +-----------+-----------+
              |                                                     |
              +-------------------+             +-------------------+
                                  |             |
                                  v             v
                           +---------------------------+
                           |  Drift Detected?          |
                           |  - Out-of-Sync: Sync      |
                           |  - Synced: Do nothing     |
                           +---------------------------+
```