# Ansible Architecture & Internal Execution Model 🏛️

Detailed breakdown of Ansible's agentless execution pipeline, Control Node architecture, module generation, and transport layers.

---

## 1. High-Level System Architecture

```
+-----------------------------------------------------------------------------------+
|                               ANSIBLE CONTROL NODE                                |
|                                                                                   |
|  +---------------------+    +---------------------+    +-----------------------+  |
|  | Ansible Playbooks   |    | Inventory File      |    | Modules & Plugins     |  |
|  | (YAML Automation)   |    | (Hosts & Groups)    |    | (Python Code Units)   |  |
|  +----------+----------+    +----------+----------+    +-----------+-----------+  |
|             |                          |                           |              |
|             +--------------------------+---------------------------+              |
|                                        |                                          |
|                                        v                                          |
|  +-----------------------------------------------------------------------------+  |
|  |                     ANSIBLE EXECUTION ENGINE & PARSER                       |  |
|  | - Generates ephemeral Python code wrappers per module task                  |  |
|  +-------------------------------------+---------------------------------------+  |
+----------------------------------------|------------------------------------------+
                                         |
                                         | Transport (OpenSSH / WinRM)
                                         v
+-----------------------------------------------------------------------------------+
|                                 MANAGED NODES                                     |
|                                                                                   |
|  +--------------------+    +--------------------+    +-------------------------+  |
|  | Web Server (Linux) |    | DB Server (Linux)  |    | Windows Host (WinRM)    |  |
|  | (Executes Python)  |    | (Executes Python)  |    | (Executes PowerShell)   |  |
|  +--------------------+    +--------------------+    +-------------------------+  |
+-----------------------------------------------------------------------------------+
```

---

## 2. Internal Execution Pipeline

When you run `ansible-playbook site.yml`, Ansible follows this step-by-step pipeline:

1. **Parse Configuration & Inventory**: Reads `ansible.cfg` and resolves target host IP addresses from inventory.
2. **Module Code Generation**: For each task, Ansible combines the module code with specified variables into a temporary Python script.
3. **Transport Transfer**: Opens SSH connection to target node and copies temporary Python script to `~/.ansible/tmp/`.
4. **Execution & Return Payload**: Executes script on target node via Python interpreter, retrieves JSON result payload, and cleans up temporary file.
5. **Idempotency Assessment**: Evaluates JSON payload (`changed: true` or `ok/changed: false`) and displays result on Control Node.

---

## 3. Key Architectural Components

- **Inventory Engine**: Supports static files (`hosts.ini`) and dynamic plugins (queries AWS EC2, GCP, Azure dynamically).
- **Variables & Fact Gathering (`setup` module)**: Automatically gathers hardware, OS, and IP facts from managed nodes prior to executing playbooks.
- **Handlers**: Special tasks triggered only when notified by another task that reported a `changed` state (e.g., restarting Nginx only if config file changed).