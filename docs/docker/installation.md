# Docker Installation Guide

This guide provides step-by-step instructions to install Docker and Docker Compose on **Linux**, **Windows**, and **macOS**.

---

## Linux Installation (Ubuntu/Debian)

Follow these steps to install Docker Engine using the official repository.

### Prerequisites

Update your existing list of packages:

```bash
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common

```

### Step 1: Add Docker’s Official GPG Key

```bash
curl -fsSL [https://download.docker.com/linux/ubuntu/gpg](https://download.docker.com/linux/ubuntu/gpg) | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

```

### Step 2: Set Up the Repository

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] [https://download.docker.com/linux/ubuntu](https://download.docker.com/linux/ubuntu) $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

```

### Step 3: Install Docker Engine

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin

```

### Step 4: Manage Docker as a Non-Root User

To run Docker commands without typing `sudo`:

```bash
sudo usermod -aG docker $USER

```

> **Note:** Log out and log back in for this change to take effect.

---

## Windows Installation

Docker Desktop on Windows uses WSL 2 (Windows Subsystem for Linux) for better performance.

### Prerequisites

* Windows 10 (64-bit: Pro, Enterprise, or Education) or Windows 11.
* WSL 2 feature enabled on Windows.

### Step 1: Download Docker Desktop

Download the official installer from the Docker website:

* [Download Docker Desktop for Windows](https://www.google.com/search?q=https://desktop.docker.com/win/main/amd64/Docker%2520Desktop%2520Installer.exe)

### Step 2: Run the Installer

1. Double-click **Docker Desktop Installer.exe** to run the wizard.
2. Ensure the **"Use WSL 2 instead of Hyper-V"** option is checked on the configuration page.
3. Follow the instructions, click **Close and restart** your computer if prompted.

### Step 3: Start Docker Desktop

* Launch **Docker Desktop** from the Start menu.
* Accept the service agreement, and wait until the Docker status icon turns green in your system tray.

---

## macOS Installation

Docker Desktop is available for both Intel and Apple Silicon (M1/M2/M3) Mac chips.

### Prerequisites

Determine your Mac chip type (Go to **Apple Menu** -> **About This Mac**).

### Step 1: Download Docker Desktop

Select the correct installer based on your processor:

* [Download for Mac with Apple Silicon (M-Series)](https://www.google.com/search?q=https://desktop.docker.com/mac/main/arm64/Docker.dmg)
* [Download for Mac with Intel Chip](https://www.google.com/search?q=https://desktop.docker.com/mac/main/amd64/Docker.dmg)

### Step 2: Installation

1. Double-click the downloaded `.dmg` file to open it.
2. Drag the **Docker** icon into your **Applications** folder.

### Step 3: Launch Docker

1. Open **Docker** from your Applications folder.
2. Give Docker privileged permissions when prompted (required for networking tools).
3. The Docker icon will appear in the top menu bar once it is running successfully.

---

## Verification

To verify that Docker and Docker Compose are installed correctly on any operating system, open your terminal (or PowerShell/Command Prompt) and run:

### Check Docker Version

```bash
docker --version

```

### Check Docker Compose Version

```bash
docker compose version

```

### Run a Test Container

```bash
docker run hello-world

```

#### Note: If you see a "Hello from Docker!" success message, your installation is complete and working correctly.