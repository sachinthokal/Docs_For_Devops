# Azure DevOps Self-Hosted Agent Setup Guide ⚙️

Guide to configuring and installing **Azure Pipelines Self-Hosted Agents** on Linux and Windows servers.

---

## 1. Installing a Self-Hosted Agent on Linux (Ubuntu / RHEL)

### Prerequisites
1. Create a **Personal Access Token (PAT)** in Azure DevOps with `Agent Pools (Read & manage)` scope.

### Step 1: Create Directory & Download Agent
```bash
# Create directory
mkdir myagent && cd myagent

# Download agent package
wget https://vstsagentpackage.azureedge.net/agent/3.238.0/vsts-agent-linux-x64-3.238.0.tar.gz

# Extract package
tar zxvf vsts-agent-linux-x64-3.238.0.tar.gz
```

### Step 2: Configure Agent
```bash
./config.sh   --unattended   --url https://dev.azure.com/YOUR-ORGANIZATION   --auth pat   --token YOUR_PAT_TOKEN   --pool Default   --agent my-linux-agent   --acceptTeeEula
```

### Step 3: Install & Start Service
```bash
# Install systemd service
sudo ./svc.sh install

# Start service
sudo ./svc.sh start
```

---

## 2. Installing a Self-Hosted Agent on Windows (PowerShell)

```powershell
# Create agent directory
New-Item -Path "C:sts-agent" -ItemType Directory; Set-Location "C:sts-agent"

# Download package
Invoke-WebRequest -Uri https://vstsagentpackage.azureedge.net/agent/3.238.0/vsts-agent-win-x64-3.238.0.zip -OutFile agent.zip

# Extract archive
Expand-Archive -Path agent.zip -DestinationPath .

# Configure agent non-interactively
.\config.cmd --unattended --url "https://dev.azure.com/YOUR-ORGANIZATION" --auth pat --token "YOUR_PAT_TOKEN" --pool "Default" --agent "win-build-agent" --runAsService

# Start service
Start-Service "vstsagent.*"
```