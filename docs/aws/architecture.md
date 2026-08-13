# AWS Architecture & Networking Layout 🏛️

Detailed breakdown of AWS Global Infrastructure, Virtual Private Cloud (VPC) networking, Identity & Access Management (IAM), and S3 Storage.

---

## 1. High-Level VPC Networking Architecture

```
+-----------------------------------------------------------------------------------+
|                                  AWS REGION                                       |
|                                                                                   |
|  +-----------------------------------------------------------------------------+  |
|  |                        VPC (Virtual Private Cloud)                          |  |
|  |                        CIDR: 10.0.0.0/16                                    |  |
|  |                                                                             |  |
|  |   +---------------------------------+   +-------------------------------+   |  |
|  |   |    AVAILABILITY ZONE A          |   |    AVAILABILITY ZONE B        |   |  |
|  |   |                                 |   |                               |   |  |
|  |   |  +---------------------------+  |   |  +-------------------------+  |   |  |
|  |   |  | Public Subnet 10.0.1.0/24 |  |   |  | Public Subnet 10.0.3.0/24|  |   |  |
|  |   |  | - Internet Gateway Route  |  |   |  | - Internet Gateway Route|  |   |  |
|  |   |  +-------------+-------------+  |   |  +-------------------------+  |   |  |
|  |   |                |                |   |                               |   |  |
|  |   |  +-------------v-------------+  |   |  +-------------------------+  |   |  |
|  |   |  | Private Subnet 10.0.2.0/24|  |   |  | Private Subnet 10.0.4.0/24|  |   |  |
|  |   |  | - App / Database Servers  |  |   |  | - App / Database Servers|  |   |  |
|  |   |  +---------------------------+  |   |  +-------------------------+  |   |  |
|  |   +---------------------------------+   +-------------------------------+   |  |
|  +-------------------------------------+---------------------------------------+  |
+----------------------------------------|------------------------------------------+
                                         |
                                         v
                            +--------------------------+
                            | INTERNET GATEWAY (IGW)   |
                            +--------------------------+
```

---

## 2. Core Architectural Components

### A. AWS IAM (Identity and Access Management)
- **Users**: Individuals or applications needing access.
- **Roles**: Temporary identities assumed by users, applications, or AWS services (e.g., EC2 assuming an IAM Role to read S3).
- **Policies**: JSON documents defining explicit `Allow` or `Deny` permissions. Implicit deny applies by default.

### B. Amazon S3 (Simple Storage Service)
- **Object Storage**: Stores unlimited unstructured data as objects inside **Buckets**.
- **Durability**: Designed for 99.999999999% (11 9s) of data durability across multiple AZs.