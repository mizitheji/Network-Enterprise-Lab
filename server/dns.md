# 🌐 DNS Server Installation Guide (dnsmasq – Ubuntu 24.04)
This document describes how to install and configure dnsmasq on Ubuntu 24.04 to provide DNS resolution for internal networks.

---

## 🧰 Overview

**dnsmasq Role**
  - Lightweight DNS forwarder & cache
  - Resolve internal hostnames
  - Forward external queries to public DNS
  - Used by:
    - Wired users
    - Wireless users
    - Guest network (via MikroTik)

**Why dnsmasq?**
  - Simple configuration
  - Low resource usage
  - Perfect for small–medium lab environments

---

## 📋 Prerequisites
  - Ubuntu 24.04 Server
  - Static IP address configured
  - Internet access
  - User with sudo privileges

---

## 🔄 1. System Update
Update the system packages:
```bash
sudo apt update
sudo apt upgrade -y
```

---

## 📦 2. Install dnsmasq
Install dnsmasq from the Ubuntu repository:
```bash
sudo apt install dnsmasq -y
```
Verify installation:
```bash
dnsmasq --version
```

---

## ⚙️ 3. Basic dnsmasq Configuration
**Backup Default Configuration**
```bash
sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.bak
```

**Create New Configuration File**
```bash
sudo nano /etc/dnsmasq.conf
```
Example basic configuration:
```bash
# Listen on specific interface
interface=ens18 # Replace with your actual interface
bind-interfaces

# Local domain
domain=lab.local
expand-hosts

# DNS cache size
cache-size=1000

# Upstream DNS servers
server=8.8.8.8
server=1.1.1.1

# Log queries (optional)
log-queries
log-facility=/var/log/dnsmasq.log
```

---

## 🧠 4. Configure Local DNS Records
Edit /etc/hosts to define local hostnames:
```bash
sudo nano /etc/hosts
```
Example:
```bash
172.20.1.1      mikrotik.lab.local
192.168.40.10   proxmox.lab.local
192.168.40.101   zabbix.lab.local
```
dnsmasq will automatically resolve these entries.

---

## 🔄 5. Restart & Enable dnsmasq
Restart dnsmasq to apply configuration:
```bash
sudo systemctl restart dnsmasq
sudo systemctl enable dnsmasq
```
Check service status:
```bash
systemctl status dnsmasq
```
**Everytime you add host in /etc/hosts, make sure you restart dnsmasq service to apply the change.**

---

## 🔍 6. Test DNS Resolution
Test DNS locally on the server:
```bash
dig zabbix.lab.local
dig google.com
```
Test from a client machine:
```cmd
nslookup zabbix.lab.local 192.168.40.101
```

---

## 🔐 7. Firewall Considerations (Optional)
Allow DNS traffic if UFW is enabled:
```bash
sudo ufw allow 53/udp
sudo ufw allow 53/tcp
```

---
