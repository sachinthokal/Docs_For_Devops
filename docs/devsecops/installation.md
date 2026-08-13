# DevSecOps Tooling Setup & Installation Guide ⚙️

Step-by-step setup guide for key open-source security tools: **Trivy**, **Gitleaks**, and **SonarQube Server**.

---

## 1. Installing Trivy (Vulnerability & Container Scanner)

### 🐧 Linux (Ubuntu / Debian)
```bash
sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy -y
```

---

## 2. Installing Gitleaks (Secret Scanner)

```bash
# Download binary directly via Shell script
wget https://github.com/gitleaks/gitleaks/releases/download/v8.18.1/gitleaks_8.18.1_linux_x64.tar.gz
tar -zxvf gitleaks_8.18.1_linux_x64.tar.gz
sudo mv gitleaks /usr/local/bin/

# Verify Installation
gitleaks version
```

---

## 3. Running SonarQube Server via Docker

```bash
# Run SonarQube Community Edition in background
docker run -d   --name sonarqube   -p 9000:9000   sonarqube:community

# Access SonarQube dashboard at http://localhost:9000 (Default login: admin / admin)
```