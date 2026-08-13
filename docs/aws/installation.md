# AWS CLI v2 Setup & Configuration Guide ⚙️

Complete guide to installing **AWS CLI v2** and configuring credentials via profiles.

---

## 1. Installing AWS CLI v2 on Linux (Ubuntu / RHEL)

```bash
# Download official AWS CLI v2 installer package
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

# Unzip installer
unzip awscliv2.zip

# Run installer executable
sudo ./aws/install

# Verify installation
aws --version
```

---

## 2. Installing AWS CLI v2 on macOS

```bash
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /
```

---

## 3. Installing AWS CLI v2 on Windows

```powershell
msiexec.exe /i https://awscli.amazonaws.com/AWSCLIV2.msi
```

---

## 4. Configuring AWS CLI Credentials

```bash
# Interactive configuration setup
aws configure
```

### Input Parameters Prompted:
- **AWS Access Key ID**: `AKIAIOSFODNN7EXAMPLE`
- **AWS Secret Access Key**: `wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY`
- **Default region name**: `us-east-1`
- **Default output format**: `json`

### Configuring Multiple Profiles
```bash
# Configure production profile
aws configure --profile production

# Test connectivity using profile
aws sts get-caller-identity --profile production
```