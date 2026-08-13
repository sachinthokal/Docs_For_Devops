# Helm Setup & Installation Guide ⚙️

Installation guide for **Helm v3** across Linux, macOS, and Windows.

---

## 1. Installing Helm CLI

### 🐧 Linux (Ubuntu / Debian)
```bash
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
sudo apt-get install apt-transport-https --yes
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm -y
```

### 🍎 macOS
```bash
brew install helm
```

### 💻 Windows
```cmd
winget install Helm.Helm
```

---

## 2. Verifying Installation & Adding Repositories

```bash
# Verify Helm version
helm version

# Add Bitnami official chart repository
helm repo add bitnami https://charts.bitnami.com/bitnami

# Update repository lists
helm repo update
```