# Azure Architecture & Resource Hierarchy 🏛️

Detailed breakdown of Azure physical and logical architecture, resource governance, networking, and the ARM deployment engine.

---

## 1. High-Level Resource Hierarchy

Azure structures access and governance across four clear management levels:

```
+-----------------------------------------------------------------------------------+
|                                 MANAGEMENT GROUPS                                 |
|               (Governance, RBAC, and Policy scopes across subscriptions)           |
+----------------------------------------+------------------------------------------+
                                         |
                                         v
+-----------------------------------------------------------------------------------+
|                                   SUBSCRIPTIONS                                   |
|                (Billing boundary, Resource quotas, Access boundary)               |
+----------------------------------------+------------------------------------------+
                                         |
                                         v
+-----------------------------------------------------------------------------------+
|                                  RESOURCE GROUPS                                  |
|          (Logical container grouping lifecycle and permissions for resources)      |
+----------------------------------------+------------------------------------------+
                                         |
                                         v
+-----------------------------------------------------------------------------------+
|                                     RESOURCES                                     |
|             (VMs, Storage Accounts, VNets, Azure SQL, Key Vaults)                 |
+-----------------------------------------------------------------------------------+
```

---

## 2. Azure Resource Manager (ARM) Engine

All interactions with Azure (Portal, CLI, PowerShell, SDKs, Terraform) flow through **Azure Resource Manager (ARM)**.

```
+-----------------------------------------------------------------------------------+
|                         CLIENTS (Portal, Azure CLI, Terraform, SDKs)              |
+----------------------------------------+------------------------------------------+
                                         | REST API Requests
                                         v
+-----------------------------------------------------------------------------------+
|                            AZURE RESOURCE MANAGER (ARM)                           |
|  - Authenticates via Microsoft Entra ID (Azure AD)                                |
|  - Enforces Role-Based Access Control (RBAC) & Azure Policies                     |
|  - Manages Locks, Tags, and Resource State                                        |
+----------------------------------------+------------------------------------------+
                                         | Dispatches to Resource Providers
                                         v
+-----------------------------------------------------------------------------------+
|                               RESOURCE PROVIDERS                                  |
|  - Microsoft.Compute  |  Microsoft.Storage  |  Microsoft.Network  |  Microsoft.Sql  |
+-----------------------------------------------------------------------------------+
```

---

## 3. Global Infrastructure Topology

- **Geography**: A discrete market containing two or more Azure Regions.
- **Region**: A set of datacenters deployed within a latency-defined perimeter connected through a dedicated low-latency network.
- **Availability Zones (AZ)**: Physically separate datacenters within an Azure region equipped with independent power, cooling, and networking.