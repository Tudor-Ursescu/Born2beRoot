# 🖥️ Born2beRoot

## 📌 Table of Contents

1. [Introduction](#-introduciton)

2. [Project Requirements](#-project-requirements)

3. [System Setup](#system-setup)

4. [Security & User Management](#-security-&-user-management)

5. [Service Configuration](#-service-configuration)

6. [Monitoring Script](#-monitoring-script)

7. [Conclusion](#-conclusion)

8. [Contributors](#-contributors)
## 📖 Introduction
Born2beRoot is a system administration project that introduces students to virtualization and server configuration using Debian (or optionally, Rocky Linux). The goal is to set up a secure, minimal server while implementing strict security policies.

## 🔧 Project Requirements
- Use VirtualBox.

- Choose between Debian (Recommended) or Rocky Linux as the operating system.

- Set up a minimal server environment (no graphical interface like X.org).

- Configure two encrypted partitions using LVM.

- Secure the system using firewalls, SSH, and user permissions.

- Implement a custom password policy.

- Configure sudo with strict security rules.

- Develop a monitoring script that displays system stats periodically.

## 🏗️ System Setup

🔹 Partitioning & LVM
- The system must have two encrypted partitions managed using LVM.
- Example partitioning:
```sh
/dev/sda1 → /boot (non-encrypted)
/dev/sda2 → LVM (encrypted, contains root and swap)
```
🔹 SSH Configuration
- SSH must run on port 4242.
- Root login over SSH must be disabled.
- Example configuration (/etc/ssh/sshd_config):
```sh
Port 4242
PermitRootLogin no
```
🔹 Firewall Setup (UFW)
- Only port 4242 should be open.
- Example commands:
```sh
sudo ufw allow 4242/tcp
sudo ufw enable
```
## 🔒 Security & User Management
🔹 Strong Password Policy
The system must enforce a `strict password policy`:
- Expiration: Every 30 days.
- Minimum age before changing: 2 days.
- Warning period: 7 days before expiry.
- Complexity:
    🔺At least 10 characters.
    🔺Contains uppercase, lowercase, and numbers.
    🔺No more than 3 consecutive identical characters.
    🔺Cannot contain the username.
- Configuration file: /etc/login.defs
```sh
PASS_MAX_DAYS   30
PASS_MIN_DAYS   2
PASS_WARN_AGE   7
```
🔹 Sudo Configuration
- Limit authentication attempts to 3.
- Enable TTY mode for security.
- Restrict paths accessible by sudo.
- Log all sudo actions.
- Example (/etc/sudoers.d/custom_sudo):
```sh
Defaults    passwd_tries=3
Defaults    logfile="/var/log/sudo/sudo.log"
Defaults    requiretty
Defaults    secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
```
## ⚙️ Service Configuration
🔹 Hostname
- The hostname must be set to your login followed by 42 (e.g., tudor42).
- Change hostname:
```sh
sudo hostnamectl set-hostname tudor42
```
🔹 User Management
- The system must have:
    🔺root user.
    🔺a user with your login (part of user42 and sudo groups).
-Example:
```sh
sudo useradd -m tudor -G sudo,user42
sudo passwd tudor
```
## 📊 Monitoring Script
🔹 Purpose
A Bash script (`monitoring.sh`) will display system stats every 10 minutes on all terminals using `wall`.
🔹 Required Information
- OS architecture and kernel version.
- Physical and virtual CPU count.
- RAM and storage usage.
- CPU load percentage.
- Last reboot time.
- LVM status.
- Active connections and logged-in users.
- IPv4 address and MAC address.
- Number of sudo commands executed.
🔹 Example Script
```sh
#!/bin/bash
wall "# Architecture: $(uname -a)
# CPU physical : $(nproc --all)
# RAM usage: $(free -m | awk 'NR==2{printf "%s/%sMB (%.2f%%)", $3, $2, $3*100/$2 }')
# Disk usage: $(df -h | awk '$NF=="/"{printf "%d/%dGB (%s)", $3, $2, $5}')
# CPU load: $(top -bn1 | grep 'Cpu(s)' | awk '{print $2}')%
# Last boot: $(who -b | awk '{print $3, $4}')
# LVM use: $(lsblk | grep -q lvm && echo yes || echo no)
# Connections TCP : $(ss -s | awk '/TCP:/ {print $2}')
# Logged users: $(who | wc -l)
# Network: IP $(hostname -I) ($(ip link show | awk '/ether/ {print $2}'))
# Sudo commands: $(journalctl _COMM=sudo | grep COMMAND | wc -l)"
```
🔹 Automating Execution (cron job)
```sh
(crontab -l; echo "*/10 * * * * /path/to/monitoring.sh") | crontab -
```
## 🎯 Conclusion
Born2beRoot is an excellent introduction to Linux system administration, covering:
✅ Virtualization (VirtualBox/UTM)

✅ Partitioning & Encryption (LVM)

✅ User Management & Security

✅ SSH & Firewall Configuration

✅ Monitoring & Automation (cron jobs, logging)

By completing this project, you will gain hands-on experience in setting up and securing a Linux server, which is a fundamental skill in DevOps, cybersecurity, and IT administration. 🚀


## 👥 Contributors
[**Tudor Ursescu**](https://github.com/Tudor-Ursescu)
