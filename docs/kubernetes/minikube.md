# Kubernetes (k8s) Minikube Installation Guide

This guide provides step-by-step instructions to install **kubectl** (Kubernetes CLI) and **Minikube** (local single-node Kubernetes cluster) on **Linux**, **Windows**, and **macOS**.

---

## Linux Installation (Ubuntu/Debian)

Follow these steps to install `kubectl` and `minikube` using Docker as the driver engine.

### Prerequisites

Update your existing list of packages and install required tools:

```bash
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl gpg

```

### Step 1: Install kubectl

```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list

sudo apt update
sudo apt install -y kubectl

```

### Step 2: Install Minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

```

### Step 3: Start the Kubernetes Cluster

Ensure Docker is running, then start Minikube:

```bash
minikube start --driver=docker

```

---

## Windows Installation

You can set up local Kubernetes using Winget package manager or via Docker Desktop.

### Prerequisites

* Windows 10/11 (64-bit).
* Docker Desktop installed with WSL 2 enabled.

### Option A: Install via Winget (Recommended)

Open **PowerShell** as Administrator and run:

#### Step 1: Install kubectl and Minikube

```powershell
winget install Kubernetes.kubectl
winget install Kubernetes.minikube

```

#### Step 2: Start Minikube

Restart your PowerShell window and run:

```powershell
minikube start

```

### Option B: Enable Kubernetes via Docker Desktop

1. Open **Docker Desktop**.
2. Go to **Settings** (gear icon) -> **Kubernetes**.
3. Check **Enable Kubernetes** and click **Apply & restart**.

---

## macOS Installation

Use Homebrew to install Kubernetes tools on both Intel and Apple Silicon Macs.

### Prerequisites

Homebrew package manager installed (`/bin/bash -c "$(curl -fsSL [https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh](https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh))"`).

### Step 1: Install kubectl and Minikube

Open Terminal and run:

```bash
brew install kubectl
brew install minikube

```

### Step 2: Start Minikube Cluster

```bash
minikube start

```

---

## Verification

To verify that `kubectl` and your local Kubernetes cluster are configured correctly on any operating system, open your terminal (or PowerShell) and run:

### Check kubectl Version

```bash
kubectl version --client

```

### Check Cluster Status

```bash
kubectl cluster-info

```

### Check Cluster Nodes

```bash
kubectl get nodes

```

### Run a Test Pod

```bash
kubectl run hello-minikube --image=registry.k8s.io/e2e-test-images/agnhost:2.39 -- /agnhost netexec --http-port=8080
kubectl get pods

```

> **Note:** If the pod status changes to `Running`, your Kubernetes installation is complete and working correctly.