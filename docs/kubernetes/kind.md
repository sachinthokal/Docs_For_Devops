# KinD (Kubernetes in Docker) Setup Guide

Here is the complete guide to install **KinD** and create both **single-node** and **multi-node (2 Worker Nodes)** Kubernetes clusters using Docker.

---

## Prerequisites

Docker must be installed and running on your system before installing KinD.

---

## Installation

### 1. Linux

```bash
# Download KinD binary
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.22.0/kind-linux-amd64

# Make it executable and move to PATH
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

```

### 2. Windows

Using **PowerShell** (via Winget or Chocolatey):

```powershell
winget install Kubernetes.kind

```

*OR via Chocolatey:*

```powershell
choco install kind

```

### 3. macOS

Using **Homebrew**:

```bash
brew install kind

```

---

## Creating Clusters

### Option 1: Quick 1-Node Cluster

To create a standard single-node cluster:

```bash
kind create cluster --name single-node-cluster

```

---

### Option 2: Multi-Node Cluster (1 Control-Plane + 2 Worker Nodes)

To create a cluster with multiple nodes in a single config, create a file named `kind-config.yaml`:

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker

```

Run the command to create the cluster using this configuration:

```bash
kind create cluster --config kind-config.yaml --name multi-node-cluster

```

---

## Verification

Run these commands to verify your KinD cluster setup:

### Check Cluster Info

```bash
kubectl cluster-info --context kind-multi-node-cluster

```

### View Connected Nodes

```bash
kubectl get nodes

```

> **Expected Output for Multi-Node:**
>
> ```text
> NAME                                 STATUS   ROLES           AGE   VERSION
> multi-node-cluster-control-plane     Ready    control-plane   1m    v1.29.2
> multi-node-cluster-worker            Ready    <none>          1m    v1.29.2
> multi-node-cluster-worker2           Ready    <none>          1m    v1.29.2
> 
> ```
>
>

---

## Useful Commands

| Action | Command |
| --- | --- |
| **List Clusters** | `kind get clusters` |
| **Delete Cluster** | `kind delete cluster --name multi-node-cluster` |
| **Load Local Docker Image** | `kind load docker-image <my-image:tag> --name multi-node-cluster` |
