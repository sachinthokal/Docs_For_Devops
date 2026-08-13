# Azure DevOps Architecture & Pipeline Mechanics 🏛️

Detailed breakdown of Azure Pipelines architecture, Execution Engines, Agent Pools, and Security Models.

---

## 1. High-Level Architecture

```
+-----------------------------------------------------------------------------------+
|                            AZURE DEVOPS SERVICE / CONTROL PLANE                   |
|                                                                                   |
|  +--------------------+    +--------------------+    +-------------------------+  |
|  |    Azure Repos     |    |   GitHub / Bitbucket |    | Variable Groups / Key   |  |
|  |   (Source Code)    |    |  (External Repos)  |    | Vault Integration       |  |
|  +---------+----------+    +---------+----------+    +------------+------------+  |
+------------|-------------------------|----------------------------|---------------+
             |                         |                            |
             +-------------------------+----------------------------+
                                       |
                                       v
+-----------------------------------------------------------------------------------+
|                           AZURE PIPELINES ORCHESTRATOR                            |
|                                                                                   |
|   - Parses `azure-pipelines.yml`                                                  |
|   - Evaluates Conditions and Stages (DAG execution engine)                        |
|   - Authenticates via Service Connections (Azure Service Principals / OIDC)        |
+--------------------------------------+--------------------------------------------+
                                       | Dispatches Tasks
                                       v
+-----------------------------------------------------------------------------------+
|                              AGENT POOL ENGINE                                    |
|                                                                                   |
|  +-----------------------------------+   +-------------------------------------+  |
|  |     Microsoft-Hosted Agents       |   |       Self-Hosted Private Agents    |  |
|  |  (Managed cloud Virtual Machines) |   | (On-premises / Azure VMs / K8s)     |  |
|  +-----------------------------------+   +-------------------------------------+  |
+-----------------------------------------------------------------------------------+
```

---

## 2. Pipeline Hierarchy Structure

```
Pipeline
 └── Stage 1: Build
      ├── Job 1: Compile Application (Agent A)
      │    ├── Step 1: Restore Dependencies
      │    └── Step 2: Build Binaries
 └── Stage 2: Deploy to Staging
      └── Job 2: Run Deployment Script (Agent B)
           └── Step 1: Deploy to Azure Web App
```

---

## 3. Key Security & Governance Features

- **Service Connections**: Allows Azure Pipelines to deploy resources to Azure without embedding passwords using **OIDC (OpenID Connect)** or Service Principals.
- **Environment & Approvals**: Add manual approval gates and check policies before a deployment stage executes.
- **Variable Groups**: Share environment variables and Azure Key Vault secrets across multiple pipelines.