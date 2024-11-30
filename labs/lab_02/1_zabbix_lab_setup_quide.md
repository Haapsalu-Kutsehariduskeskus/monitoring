# Zabbix Monitoring Lab Setup Guide

## Prerequisites
- VirtualBox installed on your computer
- Ubuntu Server 22.04 LTS ISO downloaded
- At least 8GB RAM available on host machine
- 50GB free disk space

## VM Setup Overview

### VM1: Zabbix Server
```plaintext
Name: ZabbixServer-[YourName]
Specifications:
- OS: Ubuntu Server 22.04 LTS
- RAM: 4GB
- CPU: 2 cores
- Storage: 20GB
- Network: Bridged Adapter or NAT (if Bridged doesn't work)
```

### VM2: Test Website Server
```plaintext
Name: TestWeb-[YourName]
Specifications:
- OS: Ubuntu Server 22.04 LTS
- RAM: 2GB
- CPU: 1 core
- Storage: 15GB
- Network: Same as VM1
```

## Installation Steps

### 1. Basic Setup (Both VMs)
```bash
# Update system first
sudo apt update
sudo apt upgrade -y
```

### 2. VM1 Setup (Zabbix Server)
```bash
# Install Zabbix repository
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-4%2Bubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.0-4+ubuntu22.04_all.deb
sudo apt update

# Install ALL server components
sudo apt install -y zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent mysql-server

# Setup database (Critical Steps!)
sudo mysql
mysql> create database zabbix character set utf8mb4 collate utf8mb4_bin;
mysql> create user zabbix@localhost identified by 'password';
mysql> grant all privileges on zabbix.* to zabbix@localhost;
mysql> set global log_bin_trust_function_creators = 1;
mysql> quit;

# Import schema
zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql --default-character-set=utf8mb4 -uzabbix -p zabbix

# Configure database password
sudo nano /etc/zabbix/zabbix_server.conf
DBPassword=password  # Use your chosen password

# Start services
sudo systemctl restart zabbix-server zabbix-agent apache2
sudo systemctl enable zabbix-server zabbix-agent apache2
```

### 3. VM2 Setup (Test Website)
```bash
# Install only Zabbix agent
sudo apt install -y zabbix-agent

# Configure agent
sudo nano /etc/zabbix/zabbix_agentd.conf
# Change these lines:
Server=<VM1-IP-ADDRESS>       # IP of your Zabbix server
ServerActive=<VM1-IP-ADDRESS> # Same IP
Hostname=TestWeb-YourName     # Remember this name!

# Start agent
sudo systemctl restart zabbix-agent
sudo systemctl enable zabbix-agent

# Install NGINX for test websites
sudo apt install -y nginx
```

## Accessing Zabbix

### 1. Login
```plaintext
URL: http://[VM1-IP-ADDRESS]/zabbix
Default credentials:
- Username: Admin (capital A!)
- Password: zabbix
```

### 2. Add VM2 as Host
1. Go to: Configuration → Hosts → Create host
2. Fill in:
   - Host name: TestWeb-YourName (same as in agent config!)
   - Groups: Linux servers
   - Agent interface: VM2's IP address
3. Add template: 'Linux by Zabbix agent'
4. Click Add

## Verification Steps

### 1. Test Agent Connection
```bash
# On VM1, run:
zabbix_get -s [VM2-IP] -k system.uptime
# Should return a number
```

### 2. Check Status
- Go to: Monitoring → Hosts
- Host status should be green
- Wait 2-3 minutes for initial data collection

## Troubleshooting

### Common Issues:
1. Can't connect to Zabbix web interface:
```bash
# Check Apache status
sudo systemctl status apache2
# Check logs
sudo tail -f /var/log/apache2/error.log
```

2. Agent not connecting:
```bash
# On VM2, check agent status
sudo systemctl status zabbix-agent
# Check agent logs
sudo tail -f /var/log/zabbix/zabbix_agentd.log
```

3. Database issues:
```bash
# Check MySQL status
sudo systemctl status mysql
# Check Zabbix server logs
sudo tail -f /var/log/zabbix/zabbix_server.log
```

### Network Issues:
1. If using Bridged adapter and having issues:
   - Try switching to NAT
   - Set up port forwarding in VirtualBox

2. Firewall rules:
```bash
# Allow Zabbix ports
sudo ufw allow 10050/tcp  # Agent
sudo ufw allow 10051/tcp  # Server
sudo ufw allow 80/tcp     # Web interface
```

## Required Screenshots
1. VirtualBox Manager showing both VMs
2. Successful Zabbix login screen
3. Host configuration page
4. Monitoring data from VM2

## Note
- Keep all passwords secure and documented
- Wait a few minutes after adding host for data to appear
- If using NAT, configure port forwarding for web access