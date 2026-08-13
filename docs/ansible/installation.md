# Ansible Installation & Control Node Setup ⚙️

Step-by-step setup guide for configuring an **Ansible Control Node** on Linux/macOS and establishing passwordless SSH authentication.

---

## 1. Installing Ansible on Control Node

> **Note**: Ansible Control Node must be installed on a Unix-like system (Linux, macOS, WSL on Windows). Target nodes only require SSH and Python.

### 🐧 Ubuntu / Debian
```bash
# Add official PPA
sudo apt update
sudo apt install software-properties-common -y
sudo add-apt-repository --yes --update ppa:ansible/ansible

# Install Ansible
sudo apt install ansible -y

# Verify Installation
ansible --version
```

### 🐧 RHEL / CentOS / Fedora
```bash
sudo dnf install epel-release -y
sudo dnf install ansible -y
```

### 🍎 macOS
```bash
brew install ansible
```

---

## 2. Configuring Passwordless SSH to Managed Nodes

Ansible relies on SSH key authentication to connect to managed Linux nodes seamlessly.

### Step 1: Generate SSH Key on Control Node
```bash
ssh-keygen -t ed25519 -C "ansible-control-node"
```

### Step 2: Copy Public Key to Managed Target Nodes
```bash
ssh-copy-id ubuntu@192.168.1.50
ssh-copy-id ubuntu@192.168.1.51
```

### Step 3: Test Connectivity
```bash
# Ping managed hosts using ad-hoc command
ansible all -m ping -i "192.168.1.50,192.168.1.51" -u ubuntu
```