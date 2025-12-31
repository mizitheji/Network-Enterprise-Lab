# 📊 Zabbix Installation Guide (Ubuntu 24.04)
This document describes how to install and configure Zabbix Server on Ubuntu 24.04

---

# 🧰 Overview

**Zabbix Role in This Lab**
  - Centralized network & server monitoring
  - Monitor:
    - MikroTik (SNMP)
    - Cisco C9300 / C9300L (SNMP)
    - Linux servers (Zabbix Agent)

**Stack Used**
  - Zabbix Server 7.0
  - PostgreSQL database
  - Nginx + PHP-FPM
  - Ubuntu 24.04 LTS

---

# 📋 Prerequisites
  - Ubuntu 24.04 Server
  - Static IP configured
  - Internet access
  - User with sudo privileges

---

## 🔄 1. System Update
Update the system packages before installation:
```bash
sudo apt update
sudo apt upgrade -y
```

---

## 🐘 2. Install PostgreSQL
Zabbix will use PostgreSQL as its backend database.
```bash
sudo apt install postgresql postgresql-contrib -y
```
Verify PostgreSQL is running:
```bash
systemctl status postgresql
```

---

## 📦 3. Add Zabbix Repository
Install the official Zabbix repository for Ubuntu 24.04:
```bash
wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest+ubuntu24.04_all.deb
sudo dpkg -i zabbix-release_latest+ubuntu24.04_all.deb
sudo apt update
```

---

## 🎯 4. Install Zabbix Server Components
Install Zabbix server, frontend, agent, and dependencies:
```bash
sudo apt install \
 zabbix-server-pgsql \
 zabbix-frontend-php \
 zabbix-nginx-conf \
 zabbix-sql-scripts \
 zabbix-agent \
 php8.3-pgsql -y
```

---

## 🗄️ 5. Configure PostgreSQL Database
**Create Zabbix Database & User**
```bash
sudo -u postgres createuser --pwprompt zabbix
sudo -u postgres createdb -O zabbix zabbix
```
**Import Zabbix Schema**
```bash
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix
```

---

## ⚙️ 6. Configure Zabbix Server
Edit the Zabbix server configuration file:
```bash
sudo nano /etc/zabbix/zabbix_server.conf
```
Set database parameters:
```bash
DBName=zabbix
DBUser=zabbix
DBPassword=<your database password>
```
Save and exit.

---

## 🌐 7. Configure Nginx Frontend
Edit the Zabbix Nginx configuration:
```bash
sudo nano /etc/zabbix/nginx.conf
```
Example configuration:
```bash
listen 8080;
server_name zabbix.lab.local;
```

---

## 🔄 8. Start & Enable Services
Start and enable required services:
```bash
sudo systemctl restart zabbix-server zabbix-agent nginx php8.3-fpm
sudo systemctl enable zabbix-server zabbix-agent nginx php8.3-fpm
```
Verify services:
```bash
systemctl status zabbix-server zabbix-agent nginx
```

---

## 🖥️ 9. Web Interface Setup
Open your browser and navigate to:
```bash
http://192.168.40.101:8080/zabbix
```
Follow the setup wizard:
1. Check prerequisites
2. Enter database credentials
3. Configure server details
4. Complete installation

## Default Login
```bash
Username: Admin
Password: zabbix
```

---

## 📡 10. Post-Installation Tasks
Recommended next steps:
- Enable **SNMP** on network devices
- Add hosts and templates

---

## REFERENCE
```link
https://www.howtoforge.com/how-to-install-zabbix-on-ubuntu-24-04/
```
