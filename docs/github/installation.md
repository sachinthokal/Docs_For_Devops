# GitHub Setup & Integration Guide ⚙️

This guide covers setting up GitHub accounts, installing GitHub CLI (`gh`), setting up GitHub Desktop, and configuring authentication mechanisms.

---

## 1. Account Setup & Authentication Types

To interact with GitHub securely, choose one of these primary authentication methods:

1. **SSH Key Authentication** (Best for terminal Git usage).
2. **Personal Access Token / PAT** (Required for HTTPS Git usage).
3. **GitHub CLI Auth** (Easiest and most secure command-line experience).

---

## 2. Installing GitHub CLI (`gh`)

The **GitHub CLI (`gh`)** brings Pull Requests, Issues, Actions, and Gists directly to your terminal.

### 🐧 Linux (Ubuntu / Debian)
```bash
# Add official GitHub CLI repository
type -p curl >/dev/null || (sudo apt update && sudo apt install curl -y)
curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo dd of=/usr/share/keyrings/githubcli-archive-keyring.gpg
sudo chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null

# Install gh
sudo apt update
sudo apt install gh -y
```

### 🐧 Linux (Fedora / RHEL)
```bash
sudo dnf config-manager --add-repo https://cli.github.com/packages/rpm/gh-cli.repo
sudo dnf install gh -y
```

### 🍎 macOS
```bash
brew install gh
```

### 💻 Windows
```cmd
winget install --id GitHub.cli -e --source winget
```

---

## 3. Authenticating GitHub CLI

Once installed, authenticate your command line with GitHub using the interactive login flow:

```bash
gh auth login
```

### Interactive Steps:
1. **What platform are you using?** -> `GitHub.com`
2. **What is your preferred protocol for Git operations?** -> `SSH` or `HTTPS`
3. **Generate SSH Key?** -> `Yes` (if you don't have one)
4. **How would you like to authenticate?** -> `Login with a web browser`

---

## 4. Generating a Personal Access Token (PAT)

If using HTTPS for `git push` or `git clone` without GitHub CLI:

1. Log into **GitHub.com**.
2. Go to **Settings** -> **Developer Settings** -> **Personal Access Tokens** -> **Tokens (classic)**.
3. Click **Generate new token**.
4. Set scopes (e.g., `repo`, `workflow`, `read:org`).
5. Copy token and use it as your password when prompted in Git CLI.

---

## 5. Installing GitHub Desktop (Optional)

If you prefer a graphical application for GitHub:

- **Download**: [desktop.github.com](https://desktop.github.com/)
- **Features**: Visual diffs, one-click cloning, branch switching, and direct PR creation without terminal commands.