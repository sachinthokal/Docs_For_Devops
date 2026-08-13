# Azure Management Tools Setup & Installation Guide ⚙️

Step-by-step setup guide for **Azure CLI (`az`)**, **Azure PowerShell**, and **Bicep CLI**.

---

## 1. Installing Azure CLI (`az`)

### 🐧 Linux (Ubuntu / Debian)
```bash
# Automated installation script maintained by Microsoft
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# Verify Installation
az --version
```

### 🍎 macOS
```bash
brew update && brew install azure-cli
```

### 💻 Windows
```cmd
winget install -e --id Microsoft.AzureCLI
```

---

## 2. Authenticating Azure CLI

```bash
# Log in to Azure account (opens browser window)
az login

# List accessible Subscriptions
az account list --output table

# Set active Subscription context
az account set --subscription "YOUR_SUBSCRIPTION_ID_OR_NAME"
```

---

## 3. Installing Bicep CLI (Infrastructure as Code)

Bicep is Microsoft's domain-specific language for deploying Azure resources declaratively.

```bash
# Install Bicep via Azure CLI
az bicep install

# Verify Bicep installation
az bicep version
```