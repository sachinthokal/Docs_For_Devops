# Git Installation & Configuration Guide ⚙️

This guide covers installing Git on Windows, macOS, and Linux, followed by essential first-time global configuration and SSH key integration for GitHub/GitLab.

---

## 1. Installing Git

### 🐧 Linux (Ubuntu / Debian)
```bash
# Update package lists
sudo apt update

# Install Git
sudo apt install git -y

# Verify installation
git --version
```

### 🐧 Linux (Fedora / RHEL / CentOS)
```bash
# Fedora
sudo dnf install git -y

# CentOS / RHEL
sudo yum install git -y
```

### 🐧 Linux (Arch Linux)
```bash
sudo pacman -S git
```

---

### 🍎 macOS
#### Option 1: Via Homebrew (Recommended)
```bash
# Install Homebrew if not already installed, then:
brew install git

# Verify installation
git --version
```

#### Option 2: Via Xcode Command Line Tools
```bash
xcode-select --install
```

---

## 💻 Windows

### Option 1: Official Installer (Git for Windows)
1. Download from [git-scm.com/download/win](https://git-scm.com/download/win).
2. Run the `.exe` installer.
3. Recommended settings during setup:
   - **Default Editor**: VS Code or Vim.
   - **Default Branch Name**: Choose `main`.
   - **PATH environment**: Select "Git from the command line and third-party software".
   - **Line ending conversion**: "Checkout Windows-style, commit Unix-style" (`core.autocrlf = true`).
   - **Terminal emulator**: Use MinTTY.

### Option 2: Via Winget
```cmd
winget install --id Git.Git -e --source winget
```

---

## 2. First-Time Configuration

Git uses configuration files at 3 different levels:
1. **System** (`--system`): Applies to all users on the machine (`/etc/gitconfig`).
2. **Global** (`--global`): Applies to your OS user account (`~/.gitconfig`).
3. **Local** (`--local`): Applies to the specific repository (`.git/config`).

### Essential Global Setup (Mandatory)
```bash
# Set your name (appears in commit history)
git config --global user.name "Your Name"

# Set your email (must match your GitHub/GitLab email)
git config --global user.email "your.email@example.com"

# Set default branch name to 'main'
git config --global init.defaultBranch main

# Set default text editor (VS Code, Vim, Nano, etc.)
git config --global core.editor "code --wait"

# Enable colored command output
git config --global color.ui auto

# Configure line ending preferences
# On Windows:
git config --global core.autocrlf true
# On macOS / Linux:
git config --global core.autocrlf input
```

### Verify Configuration
```bash
# View all active configuration options
git config --list

# Show configuration sources
git config --list --show-origin
```

---

## 3. SSH Key Setup for GitHub / GitLab

SSH keys allow secure authentication without typing your password/PAT repeatedly.

### Step 1: Generate a new SSH Key Pair
```bash
ssh-keygen -t ed25519 -C "your.email@example.com"
```
*Press Enter to accept default location (`~/.ssh/id_ed25519`). Optionally enter a passphrase.*

### Step 2: Start SSH Agent & Add Key
```bash
# Start background agent
eval "$(ssh-agent -s)"

# Add SSH key to agent
ssh-add ~/.ssh/id_ed25519
```

### Step 3: Copy Public Key
```bash
# Linux
cat ~/.ssh/id_ed25519.pub

# macOS
pbcopy < ~/.ssh/id_ed25519.pub

# Windows (Git Bash)
cat ~/.ssh/id_ed25519.pub | clip
```

### Step 4: Add Key to GitHub / GitLab
1. Go to **GitHub Settings** -> **SSH and GPG keys**.
2. Click **New SSH Key**.
3. Paste the key title and string, then save.

### Step 5: Test Connection
```bash
ssh -T git@github.com
```
*Expected output: `Hi username! You've successfully authenticated...`*