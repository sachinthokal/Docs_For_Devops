# GitHub Architecture & Cloud Infrastructure 🏛️

Understanding GitHub's architecture helps developers build robust automations, manage enterprise security, and structure large-scale collaboration pipelines.

---

## 1. High-Level Architecture Overview

GitHub acts as an orchestration layer on top of Git repositories. It consists of multiple cloud microservices handling authentication, storage, web UI, APIs, and background processing.

```
+-----------------------------------------------------------------------------------+
|                                 CLIENT LAYER                                      |
|  +--------------------+    +--------------------+    +-------------------------+  |
|  |   Web Dashboard    |    |  GitHub CLI (gh)   |    | Third-Party Integrations|  |
|  +---------+----------+    +---------+----------+    +------------+------------+  |
+------------|-------------------------|----------------------------|---------------+
             |                         |                            |
             | HTTPS (REST/GraphQL)    | SSH / HTTPS                | Webhooks
             v                         v                            v
+-----------------------------------------------------------------------------------+
|                             GITHUB CLOUD INFRASTRUCTURE                       |
|                                                                                   |
|   +-------------------+    +--------------------+    +------------------------+   |
|   | Authentication    |    |  API Gateway       |    |  Webhooks Engine       |   |
|   | (OAuth/SSH/PAT)   |    | (REST & GraphQL)   |    | (Event Dispatcher)     |   |
|   +---------+---------+    +---------+----------+    +-----------+------------+   |
|             |                        |                           |                |
|             v                        v                           v                |
|   +-------------------------------------------------------------------+           |
|   |                     SPOKES / GIT STORAGE CLUSTER                  |           |
|   |         (Bare Git Repositories, LFS Storage, DB Metadata)         |           |
|   +----------------------------------+--------------------------------+           |
|                                      |                                            |
|                                      v                                            |
|   +-------------------------------------------------------------------+           |
|   |                        GITHUB ACTIONS ENGINE                      |           |
|   |            (Runner Virtual Machines: Ubuntu/Windows/macOS)        |           |
|   +-------------------------------------------------------------------+           |
+-----------------------------------------------------------------------------------+
```

---

## 2. Key Architectural Components

### A. Git Storage Subsystem (Spokes)
- GitHub stores Git repositories in bare format (`.git` database contents without working trees).
- **Git LFS (Large File Storage)**: Large binary files (images, datasets, zips) are stored separately in object storage (AWS S3/Azure Blob), leaving lightweight pointers in the Git repository.

### B. Authentication & Authorization Model
- **OAuth 2.0 & Personal Access Tokens (PAT)**: Used for HTTPS Git operations and API calls.
- **SSH Keys**: Used for secure, passwordless Git CLI transport.
- **GitHub Apps**: Fine-grained permission model for bots, integrations, and continuous deployment systems.

### C. API Gateway (REST & GraphQL)
- **REST API (v3)**: Standard HTTP endpoints for CRUD operations on repos, issues, pull requests, and releases.
- **GraphQL API (v4)**: Allows fetching exact payload data in a single request, preventing over-fetching.

### D. GitHub Actions Engine
- Triggered by repository events (`push`, `pull_request`, `issue_comment`, `schedule`).
- Executes jobs in isolated ephemeral virtual machines (GitHub-hosted runners or self-hosted runners).

---

## 3. GitHub Permissions & Access Hierarchy

```
+-----------------------------------------------------------------+
|                       ORGANIZATION ACCOUNT                      |
|                                                                 |
|   +---------------------------------------------------------+   |
|   |                       TEAMS                             |   |
|   |  (e.g., @org/developers, @org/devops, @org/security)    |   |
|   +----------------------------+----------------------------+   |
|                                |                                |
|                                v                                |
|   +---------------------------------------------------------+   |
|   |                    REPOSITORIES                         |   |
|   |  - Read (Clone, View)                                   |   |
|   |  - Triage (Manage Issues, PRs)                          |   |
|   |  - Write (Push, Merge)                                  |   |
|   |  - Maintain (Manage Repo Settings)                      |   |
|   |  - Admin (Full Control, Access Rights)                  |   |
|   +---------------------------------------------------------+   |
+-----------------------------------------------------------------+
```

---

## 4. Branch Protection Rules & Code Owners

Architectural enforcement mechanisms to protect production code:

- **Branch Protection Rules**:
  - Require pull request reviews before merging.
  - Require status checks (CI tests) to pass before merging.
  - Enforce linear history (prevent merge commits if rebase is preferred).
  - Restrict who can push directly to protected branches (e.g., `main`).

- **CODEOWNERS File**:
  A `.github/CODEOWNERS` file automatically assigns specific code owners as reviewers when pull requests modify specific files or directories.