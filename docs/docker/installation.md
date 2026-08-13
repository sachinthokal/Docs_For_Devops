# Docker Installation & Setup Guide ⚙️

Complete guide to installing Docker Engine and Docker Desktop across Ubuntu Linux, macOS, and Windows.

---

## 1. Installing Docker Engine on Linux (Ubuntu / Debian)

```bash
# Update existing packages
sudo apt update && sudo apt install -y ca-certificates curl gnupg

# Add Docker official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add repository to Apt sources
echo   "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu   $(. /etc/os-release && echo "$VERSION_CODENAME") stable" |   sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine & Compose
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# Verify Installation
sudo docker run hello-world
```

### Post-Installation: Run Docker without `sudo`
```bash
# Add current user to docker group
sudo usermod -aG docker $USER

# Apply group changes
newgrp docker
```

---

## 2. Installing Docker on macOS

### Option 1: Via Homebrew Cask
```bash
brew install --cask docker
```

### Option 2: Docker Desktop Installer
1. Download installer from [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop/).
2. Drag `Docker.app` to Applications folder and launch.

---

## 3. Installing Docker on Windows

### Prerequisites
- Enable **WSL 2 (Windows Subsystem for Linux)**.

```powershell
# Run in PowerShell as Administrator
wsl --install
```

### Installation Steps
1. Download Docker Desktop for Windows installer.
2. Run `.exe` installer and ensure **Use WSL 2 instead of Hyper-V** is checked.
3. Restart computer upon completion.