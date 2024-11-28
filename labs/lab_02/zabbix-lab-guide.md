# Zabbix Monitoring Lab Setup Guide

## Prerequisites
- VirtualBox installed on your computer
- Ubuntu Server 22.04 LTS ISO downloaded
- At least 8GB RAM available on host machine
- 50GB free disk space

## VM Configuration

### VM1: Zabbix Server
```plaintext
Name: ZabbixServer-[YourName]
Specifications:
- OS: Ubuntu Server 22.04 LTS
- RAM: 4GB
- CPU: 2 cores
- Storage: 20GB
- Network: Bridged Adapter
```

### VM2: Test Website Server
```plaintext
Name: TestWeb-[YourName]
Specifications:
- OS: Ubuntu Server 22.04 LTS
- RAM: 2GB
- CPU: 1 core
- Storage: 15GB
- Network: Bridged Adapter
```

## Network Setup
1. In VirtualBox, for each VM:
   - Settings → Network
   - Adapter 1: Bridged Adapter
   - Select your physical network interface

## Operating System Installation

1. Basic Ubuntu Server Installation:
   - Choose language and keyboard layout
   - Select "Ubuntu Server"
   - Accept network configuration (DHCP)
   - Configure storage (use entire disk)
   - Complete initial setup

2. System Updates:
```bash
sudo apt update
sudo apt upgrade -y
```

## Zabbix Server Installation

1. Add Zabbix Repository:
```bash
# Download and install the Zabbix repository package
wget https://repo.zabbix.com/zabbix/6.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_6.0-4%2Bubuntu22.04_all.deb
sudo dpkg -i zabbix-release_6.0-4+ubuntu22.04_all.deb

# Update package list
sudo apt update
```

2. Install Zabbix Server and Frontend:
```bash
# Install Zabbix server, frontend, and agent
sudo apt install -y zabbix-server-mysql zabbix-frontend-php zabbix-apache2 zabbix-sql-scripts zabbix-agent

# Install MySQL server
sudo apt install -y mysql-server
```

3. Configure Database:
```bash
# Secure MySQL installation
sudo mysql_secure_installation

# Create database
sudo mysql -uroot -p
mysql> create database zabbix character set utf8mb4 collate utf8mb4_bin;
mysql> create user zabbix@localhost identified by 'password';
mysql> grant all privileges on zabbix.* to zabbix@localhost;
mysql> quit;

# Import initial schema
sudo zcat /usr/share/zabbix-sql-scripts/mysql/server.sql.gz | mysql -uzabbix -p zabbix
```

4. Configure Zabbix Server:
```bash
# Edit configuration
sudo nano /etc/zabbix/zabbix_server.conf
DBPassword=password

# Start services
sudo systemctl restart zabbix-server zabbix-agent apache2
sudo systemctl enable zabbix-server zabbix-agent apache2
```

## Test Website Setup

1. Install NGINX:
```bash
sudo apt install -y nginx
```

2. Create Test Website:
```bash
sudo nano /var/www/html/index.html
```

Add content:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Test Site</title>
</head>
<body>
    <h1>Test Website for Zabbix Monitoring</h1>
    <div id="status">Status: OK</div>
    <div id="time"></div>
    <script>
        setInterval(() => {
            document.getElementById('time').textContent = new Date().toLocaleString();
        }, 1000);
    </script>
</body>
</html>
```

## Verification Steps

1. Access Zabbix Web Interface:
   - http://[zabbix-server-ip]/zabbix
   - Default credentials:
     - User: Admin
     - Password: zabbix

2. Add Host Monitoring:
   - Configuration → Hosts → Create host
   - Add test website IP
   - Configure web monitoring scenarios

## Agent Configuration

### On Both VMs
```bash
# Install Zabbix agent
sudo apt install zabbix-agent

# Edit main configuration
sudo nano /etc/zabbix/zabbix_agentd.conf

# Change these parameters:
Server=192.168.1.x         # Your Zabbix server IP
ServerActive=192.168.1.x   # Same as Server IP
Hostname=webserver-[yourname]  # Must match name in Zabbix frontend

# Create directory for custom parameters
sudo mkdir -p /etc/zabbix/zabbix_agentd.d

# Add custom monitoring parameters
sudo nano /etc/zabbix/zabbix_agentd.d/custom_params.conf

# Add these parameters:
UserParameter=custom.nginx.status,systemctl is-active nginx
UserParameter=custom.nginx.connections,netstat -an | grep :80 | grep ESTABLISHED | wc -l
UserParameter=custom.web.response[*],curl -o /dev/null -s -w "%{time_total}" $1

# Restart agent
sudo systemctl restart zabbix-agent
sudo systemctl enable zabbix-agent

# Allow Zabbix agent port
sudo ufw allow 10050/tcp
```

### Test Agent Connection
```bash
# From Zabbix server VM, test connection to web server VM:
zabbix_get -s 192.168.1.y -k system.uptime
```

## Test Website Setup

### Create Test Website 1 (Static Content)
```bash
sudo mkdir -p /var/www/html/site1
sudo nano /var/www/html/site1/index.html
```

Add content:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Test Site 1</title>
    <style>
        body { font-family: Arial; margin: 40px; }
        .status { padding: 20px; margin: 20px 0; }
        .ok { background: #dff0d8; }
        .error { background: #f2dede; }
    </style>
</head>
<body>
    <h1>Test Site 1</h1>
    <div class="status ok" id="statusBox">Status: OK</div>
    <div>
        <p>Current Time: <span id="currentTime"></span></p>
        <button onclick="toggleStatus()">Toggle Status</button>
    </div>
    <script>
        function updateTime() {
            document.getElementById('currentTime').textContent = new Date().toLocaleString();
        }
        setInterval(updateTime, 1000);
        updateTime();

        function toggleStatus() {
            const box = document.getElementById('statusBox');
            if(box.classList.contains('ok')) {
                box.classList.replace('ok', 'error');
                box.textContent = 'Status: Error';
            } else {
                box.classList.replace('error', 'ok');
                box.textContent = 'Status: OK';
            }
        }
    </script>
</body>
</html>
```

### Create Test Website 2 (User Journey)
```bash
sudo mkdir -p /var/www/html/site2
sudo nano /var/www/html/site2/index.html
```

Add content:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Test Site 2 - User Journey</title>
    <style>
        body { font-family: Arial; margin: 40px; }
        .form-group { margin: 20px 0; }
        .step { padding: 20px; margin: 10px 0; background: #f8f9fa; }
    </style>
</head>
<body>
    <h1>Test Site 2 - User Journey</h1>
    
    <div class="step" id="step1">
        <h2>Step 1: Login</h2>
        <div class="form-group">
            <label>Username:</label>
            <input type="text" id="username">
        </div>
        <div class="form-group">
            <label>Password:</label>
            <input type="password" id="password">
        </div>
        <button onclick="nextStep(2)">Login</button>
    </div>

    <div class="step" id="step2" style="display:none;">
        <h2>Step 2: Product Selection</h2>
        <div class="form-group">
            <label>Select Product:</label>
            <select id="product">
                <option value="1">Product A</option>
                <option value="2">Product B</option>
            </select>
        </div>
        <button onclick="nextStep(3)">Add to Cart</button>
    </div>

    <div class="step" id="step3" style="display:none;">
        <h2>Step 3: Checkout</h2>
        <p>Order Summary</p>
        <div id="orderSummary"></div>
        <button onclick="complete()">Complete Order</button>
    </div>

    <script>
        function nextStep(step) {
            document.querySelectorAll('.step').forEach(el => el.style.display = 'none');
            document.getElementById('step' + step).style.display = 'block';
            
            if(step === 3) {
                const product = document.getElementById('product');
                document.getElementById('orderSummary').textContent = 
                    'Selected: ' + product.options[product.selectedIndex].text;
            }
        }

        function complete() {
            alert('Order completed successfully!');
            location.reload();
        }
    </script>
</body>
</html>
```

### Configure NGINX Virtual Hosts
```bash
sudo nano /etc/nginx/sites-available/site1
```

Add configuration:
```nginx
server {
    listen 8081;
    server_name _;
    root /var/www/html/site1;
    index index.html;
}
```

```bash
sudo nano /etc/nginx/sites-available/site2
```

Add configuration:
```nginx
server {
    listen 8082;
    server_name _;
    root /var/www/html/site2;
    index index.html;
}
```

Enable sites:
```bash
sudo ln -s /etc/nginx/sites-available/site1 /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/site2 /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

## Configure Web Monitoring in Zabbix

### Simple Website Monitoring (Site 1)
1. Go to: Configuration → Hosts → Create Host
   - Host name: TestSite1
   - Groups: Web scenarios
   - Interfaces: Agent = IP of test website VM

2. Add Web Scenario:
   - Name: Basic Website Monitoring
   - Update interval: 30s
   
3. Add Steps:
   - Step 1: Homepage Check
     - Name: Homepage
     - URL: http://[test-website-ip]:8081
     - Required status codes: 200
     - Required string: Test Site 1
   - Step 2: Status Check
     - Name: Status
     - URL: http://[test-website-ip]:8081
     - Required string: Status: OK

### User Journey Monitoring (Site 2)
1. Go to: Configuration → Hosts → Create Host
   - Host name: TestSite2
   - Groups: Web scenarios
   - Interfaces: Agent = IP of test website VM

2. Add Web Scenario:
   - Name: User Journey Monitoring
   - Update interval: 1m
   
3. Add Steps:
   - Step 1: Login Page
     - Name: Login
     - URL: http://[test-website-ip]:8082
     - Required string: Login
   - Step 2: Product Selection
     - Name: Products
     - URL: http://[test-website-ip]:8082
     - Required string: Product Selection
     Variables:
     - {username} = test
     - {password} = test
   - Step 3: Checkout
     - Name: Checkout
     - URL: http://[test-website-ip]:8082
     - Required string: Checkout

## Create Dynamic Dashboard

1. Create new dashboard:
   - Go to: Monitoring → Dashboards → Create Dashboard
   - Name: Web Monitoring Overview

2. Add widgets:
   - Widget 1: Web Monitoring Summary
     - Type: Web monitoring
     - Name: Website Status
     - Hosts: TestSite1, TestSite2
   
   - Widget 2: Problems Overview
     - Type: Problems
     - Name: Current Issues
     - Show: Web monitoring problems
   
   - Widget 3: Response Time Graph
     - Type: Graph (classic)
     - Name: Performance Overview
     - Data: Web scenario response times

3. Configure Honeycomb View:
   - Add Widget: Graph (prototype)
   - Display: Honeycomb
   - Data: Web response times
   - Color mapping:
     - Green: <1s
     - Yellow: 1-3s
     - Red: >3s
   - Configure cells:
     - Cell size: Medium
     - Metric: Response time
     - Group by: Host
     - Color by: Status

## Testing Scenarios

### Test Case 1: Availability
```bash
# Stop NGINX
sudo systemctl stop nginx

# Observe dashboard changes
# Start NGINX
sudo systemctl start nginx
```

### Test Case 2: Performance
```bash
# Add delay to NGINX
sudo nano /etc/nginx/nginx.conf
# Add in http block:
limit_rate 100k;

sudo nginx -s reload
```

### Test Case 3: Content Changes
```bash
# Modify test site content
sudo nano /var/www/html/site1/index.html
# Change "Status: OK" to "Status: Modified"

sudo systemctl reload nginx
```

## Required Screenshots for Lab Submission

1. Virtual Machine Setup:
   - VirtualBox Manager showing both VMs
   - Network settings showing bridged configuration

2. Test Website:
   - Browser showing test website 1
   - Browser showing test website 2
   - Website monitoring results in Zabbix

3. Zabbix Monitoring:
   - Web scenario configuration
   - Dynamic dashboard with widgets
   - Honeycomb visualization
   - Test scenario results

# Extra: Practical Extensions for Zabbix Lab

## 1. Web Application Monitoring Enhancement

### Extended Web Scenarios
```bash
# Add authentication monitoring
# Create login form on test site:
sudo nano /var/www/html/site2/login.php
<?php
session_start();
if ($_POST['username'] == 'test' && $_POST['password'] == 'test') {
    echo "Login successful";
} else {
    echo "Login failed";
}
?>
```

### Performance Testing
```bash
# Create load testing script
sudo apt install apache2-utils
ab -n 1000 -c 10 http://[test-website-ip]:8081/
```

## 2. System Monitoring

### Process Monitoring
```bash
# Monitor NGINX processes
sudo nano /etc/zabbix/zabbix_agentd.d/nginx.conf
UserParameter=nginx.processes,ps aux | grep nginx | wc -l
```

### Resource Usage
```bash
# Monitor disk space
UserParameter=custom.disk.usage[*],df -h $1 | tail -1 | awk '{print $5}' | sed 's/%//'
```

## 3. Log Analysis

### NGINX Log Monitoring
```bash
# Configure NGINX logging
sudo nano /etc/nginx/nginx.conf
access_log /var/log/nginx/access.log;
error_log /var/log/nginx/error.log;

# Monitor error rate
UserParameter=nginx.errors,grep -c "error" /var/log/nginx/error.log
```

### Custom Log Patterns
```bash
# Create test logs
echo "ERROR: Test error message" >> /var/log/test.log
echo "WARNING: Test warning" >> /var/log/test.log

# Monitor patterns
UserParameter=custom.log.errors,grep -c "ERROR" /var/log/test.log
UserParameter=custom.log.warnings,grep -c "WARNING" /var/log/test.log
```

## 4. Alerting System

Set-up "Alert triggers for Each Exercise"

## 5. Database Monitoring

### MySQL Basic Monitoring
```bash
# Install MySQL
sudo apt install mysql-server

# Create test database
mysql -u root -p
CREATE DATABASE testdb;
CREATE TABLE users (id INT, name VARCHAR(50));
INSERT INTO users VALUES (1, 'Test User');

# Monitor MySQL
UserParameter=mysql.table.size[*],echo "SELECT COUNT(*) FROM $1" | mysql testdb -N
```

## 6. Dashboard Creation

### Custom Graphs
```plaintext
Create combined graphs showing:
- Website response time vs NGINX process count
- Disk usage vs log file size
- Error rates over time
```

### Interactive Elements
- Add URL parameters for time range selection
- Create drill-down capabilities from overview to detailed views
- Add dynamic filtering by host groups

## 7. Security Practice

### Basic Hardening
```bash
# Secure Zabbix web server
sudo nano /etc/apache2/conf-available/security.conf
ServerTokens Prod
ServerSignature Off
TraceEnable Off

# Configure basic authentication for test sites
sudo apt install apache2-utils
sudo htpasswd -c /etc/nginx/.htpasswd admin
```

### SSL Setup
```bash
# Create self-signed certificate
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/nginx-selfsigned.key \
    -out /etc/ssl/certs/nginx-selfsigned.crt
```

## 8. Backup and Recovery

### Configuration Backup
```bash
# Backup script
sudo nano /usr/local/sbin/backup-zabbix.sh
#!/bin/bash
BACKUP_DIR="/backup/zabbix"
DATE=$(date +%Y%m%d)

# Backup configurations
tar -czf $BACKUP_DIR/config-$DATE.tar.gz /etc/zabbix/
mysqldump -u zabbix -p zabbix > $BACKUP_DIR/db-$DATE.sql

# Rotate old backups
find $BACKUP_DIR -name "*.tar.gz" -mtime +7 -delete
find $BACKUP_DIR -name "*.sql" -mtime +7 -delete
```

## 9. Performance Testing

### Load Testing
```bash
# Install stress tool
sudo apt install stress

# Create stress test
stress --cpu 2 --io 1 --vm 1 --vm-bytes 128M --timeout 300s
```

### Response Time Analysis
```bash
# Monitor response times under load
for i in {1..100}; do
  curl -o /dev/null -s -w "%{time_total}\n" http://[test-website-ip]:8081/
  sleep 1
done
```

## 10. Troubleshooting Exercises

### Common Issues
1. Service failures
```bash
# Simulate service issue
sudo systemctl stop nginx
```

2. Disk space issues
```bash
# Fill up disk space
dd if=/dev/zero of=/tmp/testfile bs=1M count=1000
```

3. High CPU usage
```bash
# Create CPU load
yes > /dev/null &
```

These exercises use only the tools and services available in your lab VMs while providing practical, hands-on experience with monitoring and system administration.
