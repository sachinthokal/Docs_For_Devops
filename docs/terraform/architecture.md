# Terraform Architecture & Internal Mechanics 🏛️

Understanding Terraform's internal engine, core split, provider plugins, state file management, and dependency graphs.

---

## 1. High-Level Architecture

Terraform architecture consists of two main parts: **Terraform Core** and **Terraform Providers**.

```
+-----------------------------------------------------------------------------------+
|                                 TERRAFORM CORE                                    |
|                                                                                   |
|  - Reads Configuration (.tf files) & State File (.tfstate)                        |
|  - Builds Directed Acyclic Graph (DAG) for Resource Dependencies                  |
|  - Calculates Execution Plan (Diff: Desired State vs Current State)               |
+-----------------------------------+-----------------------------------------------+
                                    |
                                    | gRPC RPC Calls
                                    v
+-----------------------------------------------------------------------------------+
|                               PROVIDER PLUGINS                                    |
|                                                                                   |
|  +--------------------+    +--------------------+    +-------------------------+  |
|  |    AWS Provider    |    |   Azure Provider   |    |   Kubernetes Provider   |  |
|  +---------+----------+    +---------+----------+    +------------+------------+  |
+------------|-------------------------|----------------------------|---------------+
             | API Requests            | API Requests               | API Requests
             v                         v                            v
+-----------------------------------------------------------------------------------+
|                           REAL-WORLD CLOUD INFRASTRUCTURE                         |
|        (AWS EC2, VPCs, Azure VMs, Kubernetes Clusters, GCP BigQuery)              |
+-----------------------------------------------------------------------------------+
```

---

## 2. Core Architectural Components

### A. Terraform Core
A statically compiled binary written in Go that manages:
- Parsing `.tf` configuration files.
- State file comparison and drift calculation.
- Constructing the **Resource Dependency Graph (DAG)** to determine parallel execution steps.

### B. Provider Plugins
Standalone executables that communicate with Terraform Core over RPC (gRPC). Providers translate Terraform requests into vendor-specific Cloud REST API calls (e.g., converting a `aws_instance` resource block into AWS EC2 `RunInstances` API call).

---

## 3. State Management & Remote Backends

The State file (`terraform.tfstate`) tracks metadata, mapped IDs, and dependency relationships.

### Local State vs. Remote Backend Architecture

```
LOCAL STATE (Risk of concurrency conflicts)
[ Developer 1 ] ---> ( Local .tfstate ) <--- [ Developer 2 ] (Race Conditions)

REMOTE BACKEND (Production Architecture)
[ Developer 1 ] ---\                                    /--> S3 Bucket (Encrypted .tfstate)
                    +---> [ Terraform Lock Engine ] ---+
[ Developer 2 ] ---/                                    \--> DynamoDB Table (State Lock)
```

- **Remote Backends (AWS S3, Azure Blob, Terraform Cloud)**: Store state centrally to enable team collaboration.
- **State Locking (DynamoDB / Storage Lease)**: Prevents concurrent `terraform apply` operations from corrupting state files.