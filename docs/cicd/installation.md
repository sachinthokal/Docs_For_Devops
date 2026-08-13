# CI/CD Engine Installations & Setup ⚙️

Step-by-step installation guides for popular self-hosted CI/CD automation tools: **Jenkins** and **GitHub Actions Self-Hosted Runners**.

---

## 1. Installing Jenkins via Docker (Recommended)

```bash
# Create a dedicated network for Jenkins
docker network create jenkins

# Run Jenkins Controller container
docker run -d   --name jenkins-controller   --network jenkins   -p 8080:8080 -p 50000:50000   -v jenkins_home:/var/jenkins_home   jenkins/jenkins:lts-jdk17

# Retrieve initial administrator password
docker exec jenkins-controller cat /var/jenkins_home/secrets/initialAdminPassword
```
*Access Jenkins Dashboard in browser at `http://localhost:8080`.*

---

## 2. Installing GitLab Runner on Linux

```bash
# Download GitLab Runner official repository script
curl -L "https://packages.gitlab.com/install/repositories/runner/gitlab-runner/script.deb.sh" | sudo bash

# Install GitLab Runner
sudo apt install gitlab-runner -y

# Register Runner with your GitLab Instance
sudo gitlab-runner register   --url "https://gitlab.com/"   --registration-token "YOUR_REGISTRATION_TOKEN"   --executor "docker"   --docker-image "alpine:latest"
```

---

## 3. Configuring GitHub Actions Self-Hosted Runner

1. Go to your **GitHub Repository** -> **Settings** -> **Actions** -> **Runners**.
2. Click **New self-hosted runner**.
3. Run the generated commands on your server:

```bash
# Create a folder
mkdir actions-runner && cd actions-runner

# Download latest runner package
curl -o actions-runner-linux-x64-2.311.0.tar.gz -L https://github.com/actions/runner/releases/download/v2.311.0/actions-runner-linux-x64-2.311.0.tar.gz

# Extract installer
tar xzf ./actions-runner-linux-x64-2.311.0.tar.gz

# Configure & Run
./config.sh --url https://github.com/owner/repo --token YOUR_RUNNER_TOKEN
sudo ./svc.sh install
sudo ./svc.sh start
```