# Linux

This roadmap is designed to take you from a Linux beginner to a professional who can confidently manage servers, automate tasks, and deploy applications in a DevOps environment.

---

## Phase 1: The Absolute Basics (Foundations)

Before jumping into automation, you need to feel comfortable moving around the command line.

### 1. Essential Navigation & File Management

* **Concepts:** Understanding the Linux Directory Structure (Everything is a file, `/etc`, `/var`, `/opt`, `/home`).
* **Commands to Master:**
* `pwd` (Print working directory)
* `ls -la` (List files with details and hidden files)
* `cd` (Change directory)
* `mkdir -p` (Create nested directories)
* `touch`, `cp`, `mv`, `rm -rf` (Create, copy, move, and forcefully delete files/folders)

### 2. Viewing and Editing Files

* **Concepts:** Quick viewing vs. interactive editing.
* **Commands to Master:**
* `cat`, `less`, `head`, `tail -f` (Crucial for monitoring live log files)
* **Vim or Nano:** Learn at least one terminal-based text editor. (Know how to open, edit, save, and quit in Vim).

---

## Phase 2: User Management & Permissions (Security)

In DevOps, you must ensure applications and users have only the minimum required access (Principle of Least Privilege).

### 1. File Permissions

* **Concepts:** Read ($r=4$), Write ($w=2$), Execute ($x=1$) for User, Group, and Others.
* **Commands to Master:**
* `chmod` (Change file permissions, e.g., `chmod 755 script.sh`)
* `chown` (Change file owner and group)

### 2. User & Sudo Administration

* **Concepts:** Root user vs. standard user, giving temporary admin rights.
* **Commands to Master:**
* `useradd`, `usermod`, `userdel`
* `passwd` (Change password)
* `sudo` (Execute command as root)
* Understanding the `/etc/passwd` and `/etc/sudoers` files.

---

## Phase 3: Text Processing & Package Management

DevOps engineers deal with massive configuration files and logs. You need to know how to filter data quickly.

### 1. Searching & Filtering

* **Commands to Master:**
* `grep` / `egrep` (Search for text patterns inside files)
* `find` (Locate files based on name, size, or time)
* `awk` & `sed` (Basic stream editing and data extraction)
* **Pipes (`|`) and Redirections (`>`, `>>`):** Passing the output of one command as input to another.

### 2. Package Management

* **Concepts:** Installing, updating, and removing software dependencies.
* **Tools to Master:**
* **Ubuntu/Debian:** `apt update`, `apt install`, `apt remove`
* **CentOS/RHEL:** `yum` or `dnf` equivalents

---

## Phase 4: Networking & Troubleshooting

Applications talk to each other over networks. When a deployment fails, you need to find out why.

### 1. Network Inspection

* **Concepts:** IP addresses, ports, DNS, and firewalls.
* **Commands to Master:**
* `ping` (Check connectivity)
* `curl -v` / `wget` (Test API endpoints and download files)
* `netstat -tulnp` or `ss -tulnp` (Check which applications are listening on which ports)
* `nslookup` / `dig` (Troubleshoot DNS issues)
* `ip a` or `ifconfig` (Check system network interfaces)

---

## Phase 5: Process Management & System Performance

How to monitor applications running on your server and deal with resource overloads.

### 1. Managing Running Services

* **Concepts:** Systemd init system.
* **Commands to Master:**
* `systemctl start | stop | restart | status <service>`
* `systemctl enable | disable <service>` (Ensure service starts on boot)

### 2. Resource Monitoring

* **Commands to Master:**
* `top` / `htop` (Live CPU and memory usage)
* `ps aux | grep <process_name>` (Find process IDs)
* `kill -9 <PID>` (Force stop a hanging process)
* `df -h` (Check available disk space)
* `free -m` (Check RAM usage)

---

## Phase 6: Automation with Shell Scripting (The DevOps Core)

Stop doing things manually. If you do it more than twice, automate it.

### 1. Scripting Fundamentals

* **Topics to Learn:**
* Writing your first `#!/bin/bash` script.
* Variables and Environment Variables (`export ENV="production"`).
* Positional arguments (`$1`, `$2`).
* Conditional statements (`if/else`) and Loops (`for`, `while`).
* Exit codes (`exit 0` vs `exit 1`).

### 2. Cron Jobs (Scheduling)

* **Concepts:** Automating backups, log rotations, or cleanups at specific times.
* **Commands to Master:**
* `crontab -e` (Edit cron jobs)
* Understanding the crontab syntax: `* * * * * /path/to/script.sh`

---

## Phase 7: Advanced Linux for DevOps (Bridge to Cloud/Containers)

Once you know the OS, learn how it powers modern infrastructure.

### 1. SSH (Secure Shell)

* **Concepts:** Passwordless login, managing remote servers safely.
* **Skills to Master:**
* Generating keys: `ssh-keygen`
* Copying keys to server: `ssh-copy-id user@remote_ip`
* Configuring `~/.ssh/config` for easy access.

### 2. Linux Namespaces & Cgroups (Docker Foundations)

* **Concepts:** Docker isn't magic; it uses native Linux features.
* *Namespaces:* Isolate process views (Network, Mount, PID).
* *Cgroups (Control Groups):* Limit resource usage (CPU, Memory).

---

## Recommended Practice Project

To lock in your knowledge, set up a local Linux VM (using VirtualBox or AWS EC2 free tier) and do this:

1. Deploy an **Nginx Web Server**.
2. Change its default configuration port to `8080`.
3. Write a Bash script that checks if Nginx is running; if it is down, the script should automatically restart it.
4. Schedule this script to run every 5 minutes using a **Cron Job**.
