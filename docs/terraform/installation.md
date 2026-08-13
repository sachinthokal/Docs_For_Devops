# Terraform Installation & Setup Guide ⚙️

Installation guide for Terraform CLI across Linux, macOS, and Windows, including `tfenv` version manager installation.

---

## 1. Installing Terraform CLI

### 🐧 Linux (Ubuntu / Debian)
```bash
# Install HashiCorp GPG key & repository
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

# Install Terraform
sudo apt update && sudo apt install terraform -y

# Verify Installation
terraform -version
```

### 🍎 macOS
```bash
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

### 💻 Windows
```powershell
# Via Chocolatey
choco install terraform

# Via Winget
winget install HashiCorp.Terraform
```

---

## 2. Recommended: Installing `tfenv` (Terraform Version Manager)

`tfenv` allows switching seamlessly between multiple Terraform versions across different projects.

```bash
# Clone tfenv repository
git clone --depth=1 https://github.com/tfutils/tfenv.git ~/.tfenv

# Add to PATH (~/.bashrc or ~/.zshrc)
echo 'export PATH="$HOME/.tfenv/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Install and switch Terraform versions
tfenv install 1.7.0
tfenv use 1.7.0
```