# GitHub Actions Architecture & Internal Working 🏛️

Understanding how GitHub Actions schedules jobs, manages runner environments, and handles events under the hood.

---

## 1. High-Level System Architecture

```
+-----------------------------------------------------------------------------------+
|                                GITHUB CLOUD / EVENT LAYER                         |
|                                                                                   |
|  +--------------------+    +--------------------+    +-------------------------+  |
|  | Git Push / PR Event|    | Scheduled Cron Job |    | Manual dispatch Event   |  |
|  +---------+----------+    +---------+----------+    +------------+------------+  |
+------------|-------------------------|----------------------------|---------------+
             |                         |                            |
             +-------------------------+----------------------------+
                                       | Dispatches
                                       v
+-----------------------------------------------------------------------------------+
|                        GITHUB ACTIONS ORCHESTRATION ENGINE                         |
|                                                                                   |
|   - Parses `.github/workflows/*.yml`                                              |
|   - Resolves Dependency Graph (`needs:` keyword)                                  |
|   - Inject Encrypted Secrets & Environment Variables                              |
+--------------------------------------+--------------------------------------------+
                                       | Assigns Jobs
                                       v
+-----------------------------------------------------------------------------------+
|                            RUNNER EXECUTION POOL                                  |
|                                                                                   |
|  +-----------------------------------+   +-------------------------------------+  |
|  |     GitHub-Hosted Runners         |   |         Self-Hosted Runners         |  |
|  |  (Ephemeral Ubuntu/Win/macOS VMs)  |   | (On-Premises / Private Cloud / K8s) |  |
|  +-----------------------------------+   +-------------------------------------+  |
+-----------------------------------------------------------------------------------+
```

---

## 2. Core Architectural Components

### A. Event Dispatcher
Monitors repository activities and webhooks. When an event matches the `on:` trigger rules defined in a workflow file, the engine initializes a execution instance.

### B. Runner Models

| Feature | GitHub-Hosted Runners | Self-Hosted Runners |
| :--- | :--- | :--- |
| **Maintenance** | Managed completely by GitHub | Managed and maintained by user |
| **Clean State** | Fresh ephemeral VM created for every job | State can persist between jobs unless configured |
| **Network Access** | Public Internet | Direct access to internal private networks / VPN |
| **Customization** | Standard pre-installed tools | Customizable hardware, OS, and software |

### C. Action Types
1. **JavaScript Actions**: Run directly on the runner host machine (fast execution).
2. **Docker Container Actions**: Run inside an isolated Docker container on Linux runners.
3. **Composite Actions**: Combine multiple workflow steps into a single reusable action.