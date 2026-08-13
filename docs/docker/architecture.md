# Docker Architecture & Internals 🏛️

Understanding Docker's internal architecture helps in designing secure, high-performance containerized systems.

---

## 1. High-Level Architecture

Docker uses a **client-server architecture**. The Docker Client communicates with the Docker Daemon, which performs the heavy lifting of building, running, and distributing containers.

```
+-----------------------------------------------------------------------------------+
|                                 DOCKER HOST                                       |
|                                                                                   |
|  +--------------------+               +----------------------------------------+  |
|  |   Docker Client    |               |             Docker Daemon              |  |
|  |  (docker CLI/GUI)  |               |               (dockerd)                |  |
|  +---------+----------+               +-------------------+--------------------+  |
|            |                                              |                       |
|            | REST API / Unix Socket                       | Manages               |
|            v                                              v                       |
|  +--------------------+               +----------------------------------------+  |
|  | Containerd / Runc  | <------------ | Images, Containers, Networks, Volumes  |  |
|  +--------------------+               +----------------------------------------+  |
+-----------------------------------------------------------------------------------+
                                                            ^
                                                            | Pulls / Pushes Images
                                                            v
                                                +------------------------+
                                                |    Docker Registry     |
                                                |  (Docker Hub/ECR/GCR)  |
                                                +------------------------+
```

---

## 2. Core Architectural Components

### A. Docker Client
The primary interface used by developers (`docker run`, `docker build`). It translates commands into REST API calls and sends them to the Docker Daemon.

### B. Docker Daemon (`dockerd`)
A persistent background process that listens for Docker API requests and manages Docker objects such as images, containers, networks, and volumes.

### C. Containerd & Runc
- **Containerd**: An industry-standard container runtime that manages the complete container lifecycle (image transfer, storage, execution, monitoring).
- **Runc**: A lightweight, OCI-compliant tool used by `containerd` to spawn and run containers according to Linux kernel standards.

---

## 3. Underlying Linux Kernel Technologies

Docker relies on key Linux kernel capabilities to create isolation:

- **Namespaces**: Provide isolation for process trees (PID), networking (NET), mount points (MNT), and user IDs (USER).
- **Control Groups (cgroups)**: Limit and measure resource usage (CPU, Memory, Disk I/O) per container.
- **Union File Systems (Overlay2)**: Allows layers to be stacked and combined into a single unified view.

---

## 4. Docker Image Layers & Storage Drivers

Docker images are composed of read-only layers. When a container starts, Docker adds a thin **Read-Write Container Layer** on top.

```
+----------------------------------------------------+
|  Container Read-Write Layer (App logs, temp data)  |  <- Created on 'docker run'
+----------------------------------------------------+
|  Image Layer 3: CMD ["node", "app.js"]            |  (Read-Only)
+----------------------------------------------------+
|  Image Layer 2: COPY . /app                        |  (Read-Only)
+----------------------------------------------------+
|  Image Layer 1: RUN npm install                    |  (Read-Only)
+----------------------------------------------------+
|  Base Layer: FROM node:18-alpine                   |  (Read-Only)
+----------------------------------------------------+
```