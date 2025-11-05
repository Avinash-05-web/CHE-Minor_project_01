🛡️ Cloud Server Hardening & Secure Access (SSH + Firewall + IAM)
Author: Avinash Das Manikpuri
Course: CY-5A – Ethical Hacking Project
ERP:6604666
Title: Cloud Server Hardening & Secure Access
Date: November 2025

📘 Project Overview
This project demonstrates comprehensive Linux server hardening by deploying, configuring, and securing an Ubuntu-based cloud server.
It enforces least-privilege access, SSH key-based authentication, firewall rules (UFW), and intrusion prevention with Fail2Ban — all monitored by Auditd for accountability.

🎯 Objectives


Harden Ubuntu Server to minimize attack surfaces.


Enforce least privilege access using IAM-like user management.


Disable root SSH login and password authentication.


Configure UFW firewall to allow only essential traffic.


Protect against brute-force SSH attacks with Fail2Ban.


Enable Auditd for logging and auditing user activity.



🧰 Prerequisites


Ubuntu Server 20.04 or 22.04 (Cloud VM / VMware / VirtualBox)


SSH Client (Linux/macOS Terminal or Windows PowerShell)


Local machine for generating SSH keys


Internet access for installation



⚙️ Step-by-Step Implementation
1️⃣ Update & Install Essentials
sudo apt update && sudo apt upgrade -y    # Update all system packages
sudo apt install -y wget curl git vim     # Install essential utilities


2️⃣ Install & Start SSH Server (if missing)
sudo apt install -y openssh-server        # Install SSH server
sudo systemctl enable --now ssh           # Enable and start SSH service
hostname -I                               # Display server IP for SSH connection


3️⃣ Create a New Administrative User
Replace projectuser01 with your preferred username.
sudo adduser projectuser01
# Follow prompts to set password and optional info

# Grant sudo privileges via dedicated sudoers file
echo "projectuser01 ALL=(ALL:ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/projectuser01 > /dev/null
sudo chmod 440 /etc/sudoers.d/projectuser01

# Verify sudo permissions
sudo cat /etc/sudoers.d/projectuser01


4️⃣ SSH Hardening (Disable Root Login + Password Auth)
sudo sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo sed -i 's/^#\?PasswordAuthentication.*/PasswordAuthentication no/' /etc/ssh/sshd_config
sudo systemctl restart ssh

# Verify SSH security settings
grep -Ei 'PermitRootLogin|PasswordAuthentication' /etc/ssh/sshd_config


5️⃣ Generate SSH Key & Configure Authentication
On your local machine:
ssh-keygen -t rsa -b 4096 -f ~/.ssh/project_key_$(date +%Y%m%d) -C "project_submission"

Copy the public key to your server (recommended):
ssh-copy-id -i ~/.ssh/project_key_$(date +%Y%m%d).pub projectuser01@<SERVER_IP>

Or manually configure it on the server:
sudo mkdir -p /home/projectuser01/.ssh
sudo nano /home/projectuser01/.ssh/authorized_keys   # Paste public key here

sudo chown -R projectuser01:projectuser01 /home/projectuser01/.ssh
sudo chmod 700 /home/projectuser01/.ssh
sudo chmod 600 /home/projectuser01/.ssh/authorized_keys


6️⃣ Configure Firewall (UFW)
sudo apt install -y ufw
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow OpenSSH               # Allow SSH (port 22)
sudo ufw enable
sudo ufw status verbose


7️⃣ Install & Configure Fail2Ban
sudo apt install -y fail2ban
sudo systemctl enable --now fail2ban
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

Edit the configuration file:
sudo nano /etc/fail2ban/jail.local

Ensure the [sshd] section looks like this:
[sshd]
enabled = true
port    = ssh
filter  = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600

Then restart the service:
sudo systemctl restart fail2ban
sudo fail2ban-client status sshd
sudo tail -n 200 /var/log/fail2ban.log

Simulate a brute-force attempt (from another IP or user) and verify ban:
sudo tail -f /var/log/auth.log


8️⃣ Install & Enable Auditd for Logging
sudo apt install -y auditd audispd-plugins
sudo systemctl enable --now auditd

Add specific audit rules:
# Watch sudo command executions
sudo auditctl -w /usr/bin/sudo -p x -k sudo_exec

# Watch authentication log changes
sudo auditctl -w /var/log/auth.log -p wa -k authlog

View today’s logs:
sudo ausearch -k sudo_exec -ts today
sudo ausearch -k authlog -ts today


🧩 Optional (Advanced Monitoring)
Integrate with Wazuh or any SIEM tool for centralized logging and alerting.


🧠 Key Learnings


Created and managed admin users securely with sudo privileges.


Disabled root SSH access and enforced key-only authentication.


Configured UFW to restrict traffic and reduce attack surface.


Deployed Fail2Ban to prevent brute-force login attempts.


Implemented Auditd to log user and sudo command activities.


Gained real-world experience in Linux server security & monitoring.



✅ Conclusion
This project successfully demonstrates secure Linux cloud server configuration using open-source tools.
It ensures that only authenticated users gain access while maintaining a robust defense through logging, firewalling, and intrusion prevention — forming the foundation of modern server hardening best practices.

📁 Project File
📄 Cloud Server Hardening Project (Avinash Das Manikpuri) CY-5A.pdf
Contains full documentation, explanations, and results of the above configuration.

📌 License
This project is intended for academic and ethical use only.
Unauthorized or malicious use of these configurations is strictly prohibited.

Or format it into a printable PDF-style academic README (with cover header and signature space)?

Is this conversation helpful so far?
