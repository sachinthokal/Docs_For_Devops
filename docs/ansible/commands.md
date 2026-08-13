# Ansible Commands & Playbook Syntax Reference ⚡

Categorized guide covering Ansible ad-hoc commands, Playbooks, Ansible Vault, and Roles.

---

## 1. Ad-Hoc Commands (`ansible`)

Ad-hoc commands execute one-off tasks without writing full playbooks.

```bash
# Test connectivity to all hosts in inventory
ansible all -m ping -i inventory.ini

# Check disk space on webservers group
ansible webservers -m command -a "df -h" -i inventory.ini

# Install Nginx package on webservers group using apt
ansible webservers -m apt -a "name=nginx state=present update_cache=yes" --become -i inventory.ini

# Restart a service
ansible webservers -m service -a "name=nginx state=restarted" --become -i inventory.ini
```

---

## 2. Inventory File Example (`hosts.ini`)

```ini
[webservers]
web1.example.com ansible_host=192.168.1.50
web2.example.com ansible_host=192.168.1.51

[dbservers]
db1.example.com ansible_host=192.168.1.60

[all:vars]
ansible_user=ubuntu
ansible_ssh_private_key_file=~/.ssh/id_ed25519
```

---

## 3. Sample Ansible Playbook (`setup_web.yml`)

```yaml
---
- name: Configure Web Servers
  hosts: webservers
  become: true  # Run with sudo privileges

  vars:
    http_port: 80
    max_clients: 200

  tasks:
    - name: Install Nginx Web Server
      ansible.builtin.apt:
        name: nginx
        state: present
        update_cache: yes

    - name: Ensure Nginx service is running and enabled
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true

    - name: Deploy custom index.html page
      ansible.builtin.copy:
        content: "<h1>Managed by Ansible</h1>"
        dest: /var/www/html/index.html
        mode: '0644'
      notify: Restart Nginx

  handlers:
    - name: Restart Nginx
      ansible.builtin.service:
        name: nginx
        state: restarted
```

---

## 4. Playbook Execution & Vault Commands

```bash
# Run a Playbook
ansible-playbook -i hosts.ini setup_web.yml

# Check syntax of Playbook without executing
ansible-playbook -i hosts.ini setup_web.yml --syntax-check

# Dry run (Preview changes)
ansible-playbook -i hosts.ini setup_web.yml --check

# Ansible Vault (Encrypt Secrets/Passwords)
ansible-vault create vars/secrets.yml
ansible-vault encrypt vars/secrets.yml
ansible-vault edit vars/secrets.yml
ansible-playbook -i hosts.ini setup_web.yml --ask-vault-pass
```