# GitHub Actions Runner Installation Guide ⚙️

Step-by-step installation and service setup guide for **Self-Hosted Runners** across Linux, Windows, and macOS environments.

---

## 1. Installing a Self-Hosted Runner on Ubuntu / Debian Linux

### Step 1: Download Runner Package
```bash
# Create directory
mkdir actions-runner && cd actions-runner

# Download latest runner package
curl -o actions-runner-linux-x64-2.317.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.317.0/actions-runner-linux-x64-2.317.0.tar.gz

# Extract installer
tar xzf ./actions-runner-linux-x64-2.317.0.tar.gz
```

### Step 2: Configure Runner
```bash
# Register runner using token generated in GitHub (Repo -> Settings -> Actions -> Runners)
./config.sh --url https://github.com/YOUR-OWNER/YOUR-REPO --token YOUR_RUNNER_REGISTRATION_TOKEN
```

### Step 3: Install & Start as Systemd Service
```bash
# Install systemd service
sudo ./svc.sh install

# Start service
sudo ./svc.sh start

# Check service status
sudo ./svc.sh status
```

---

## 2. Installing a Self-Hosted Runner on Windows (PowerShell)

```powershell
# Create folder
New-Item -Path "C:ctions-runner" -ItemType Directory; Set-Location "C:ctions-runner"

# Download runner package
Invoke-WebRequest -Uri https://github.com/actions/runner/releases/download/v2.317.0/actions-runner-win-x64-2.317.0.zip -OutFile actions-runner.zip

# Extract archive
Expand-Archive -Path actions-runner.zip -DestinationPath .

# Configure runner
.\config.cmd --url https://github.com/YOUR-OWNER/YOUR-REPO --token YOUR_RUNNER_REGISTRATION_TOKEN

# Install and start as Windows Service
.un.cmd
```