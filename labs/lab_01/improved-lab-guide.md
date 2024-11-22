# Linux Logging Infrastructure Lab
## Overview

This hands-on lab teaches you how to build a secure, production-grade logging infrastructure. You'll implement centralized logging with real-time monitoring and analysis capabilities.

### Learning Objectives
- Build a secure centralized logging system
- Implement log monitoring and analysis
- Configure secure log rotation and retention
- Create automated security monitoring

### Prerequisites
- VirtualBox/VMware Workstation
- 4GB free RAM minimum
- 50GB free storage space
- Basic Linux command-line experience

## Environment Setup

### 1. Virtual Machine Configuration

#### VM1: Log Server
```bash
Name: LogServer
RAM: 2GB
Storage: 20GB
OS: Ubuntu 22.04 LTS or AlmaLinux 9
Network: NAT + Host-only adapter
```

#### VM2: Client
```bash
Name: LogClient
RAM: 1GB
Storage: 20GB
OS: Same as LogServer
Network: NAT + Host-only adapter
```

### 2. Initial System Configuration

Run these commands on both VMs:

```bash
#!/bin/bash
# save as setup.sh

# Detect OS and set package manager
if [ -f /etc/debian_version ]; then
    PKG_MANAGER="apt"
    PKG_UPDATE="$PKG_MANAGER update && $PKG_MANAGER upgrade -y"
    PKG_INSTALL="$PKG_MANAGER install -y"
elif [ -f /etc/redhat-release ]; then
    PKG_MANAGER="dnf"
    PKG_UPDATE="$PKG_MANAGER update -y"
    PKG_INSTALL="$PKG_MANAGER install -y"
else
    echo "Unsupported OS"
    exit 1
fi

# Update system
echo "Updating system..."
sudo $PKG_UPDATE

# Install required packages
echo "Installing required packages..."
sudo $PKG_INSTALL rsyslog logrotate acl syslog-ng gawk bc sysstat net-tools

# Verify installations
for cmd in rsyslogd logrotate gawk bc sar netstat; do
    if ! command -v $cmd &> /dev/null; then
        echo "ERROR: $cmd not installed"
        exit 1
    fi
done

echo "Setup completed successfully"
```

### 3. Security Setup

```bash
#!/bin/bash
# save as security_setup.sh

set -e  # Exit on any error

# Create logging user and group
echo "Creating logging user and group..."
sudo groupadd -f loggroup
sudo useradd -r -s /sbin/nologin -g loggroup -G adm,syslog loguser

# Create log directories with secure permissions
echo "Setting up log directories..."
sudo mkdir -p /var/log/clients/{auth,system,app}
sudo mkdir -p /var/log/archive

# Set ownership
sudo chown -R loguser:loggroup /var/log/clients
sudo chown loguser:loggroup /var/log/archive

# Set directory permissions
sudo find /var/log/clients -type d -exec chmod 750 {} \;
sudo find /var/log/clients -type f -exec chmod 640 {} \;
sudo chmod 750 /var/log/archive

# Set up ACLs
sudo setfacl -m u:loguser:r /etc/rsyslog.conf
sudo setfacl -m u:loguser:r-x /etc/rsyslog.d/

# Verify setup
echo "Verifying setup..."
ls -la /var/log/clients
getfacl /etc/rsyslog.conf
id loguser

echo "Security setup completed"
```

## Part 1: Log Server Setup (VM1)

### 1.1 Configure Central Log Reception

```bash
# Create rsyslog configuration
sudo tee /etc/rsyslog.d/10-central-server.conf > /dev/null << 'EOF'
# Enable TCP and UDP reception
module(load="imudp")
module(load="imtcp")

# Enable TLS for secure log transmission
module(load="imtls")

# Configure ports
input(type="imudp" port="514")
input(type="imtcp" port="514")
input(type="imtls" port="6514")

# Set up templates for structured logging
template(name="ClientLogs" type="string" string="/var/log/clients/%HOSTNAME%/%PROGRAMNAME%.log")

# Routing rules
if $fromhost-ip startswith '192.168.' then {
    auth.* ?ClientLogs
    *.* /var/log/clients/catch-all.log
    stop
}
EOF

# Restart rsyslog
sudo systemctl restart rsyslog
```

### 1.2 Set Up Log Rotation

```bash
# Create logrotate configuration
sudo tee /etc/logrotate.d/client-logs > /dev/null << 'EOF'
/var/log/clients/*/*.log {
    daily
    rotate 7
    compress
    delaycompress
    missingok
    notifempty
    create 0640 loguser loggroup
    sharedscripts
    postrotate
        systemctl reload rsyslog
    endscript
    size 50M
    dateext
    dateformat -%Y%m%d-%H%M%S
}
EOF
```

### 1.3 Implement Log Analysis Dashboard

```bash
# Save as /opt/monitoring/log_dashboard.sh
#!/bin/bash

set -e

# Constants
readonly REFRESH_RATE=10
readonly LOG_DIR="/var/log/clients"
readonly ANALYSIS_WINDOW=300  # 5 minutes in seconds

# Color definitions
readonly RED='\033[0;31m'
readonly GREEN='\033[0;32m'
readonly YELLOW='\033[1;33m'
readonly NC='\033[0m'

# Function to analyze logs
analyze_logs() {
    local start_time=$(($(date +%s) - ANALYSIS_WINDOW))
    
    # Initialize counters
    local errors=0
    local warnings=0
    local info=0
    local auth_failures=0
    
    # Process each log file
    while IFS= read -r -d '' logfile; do
        while IFS= read -r line; do
            local timestamp=$(date -d "$(echo "$line" | awk '{print $1,$2,$3}')" +%s 2>/dev/null)
            
            if [[ $timestamp -ge $start_time ]]; then
                case "$line" in
                    *ERROR*) ((errors++));;
                    *WARNING*) ((warnings++));;
                    *INFO*) ((info++));;
                    *"authentication failure"*) ((auth_failures++));;
                esac
            fi
        done < "$logfile"
    done < <(find "$LOG_DIR" -type f -name "*.log" -print0)
    
    # Display results
    echo -e "${RED}Errors (last 5 min): $errors${NC}"
    echo -e "${YELLOW}Warnings: $warnings${NC}"
    echo -e "${GREEN}Info: $info${NC}"
    echo "Auth Failures: $auth_failures"
}

# Main loop
main() {
    while true; do
        clear
        echo "=== Log Analysis Dashboard ==="
        echo "Updated: $(date '+%Y-%m-%d %H:%M:%S')"
        echo "--------------------------------"
        
        analyze_logs
        
        # Show disk usage
        echo "--------------------------------"
        echo "Storage Status:"
        df -h "$LOG_DIR"
        
        sleep "$REFRESH_RATE"
    done
}

main
```

## Part 2: Client Setup (VM2)

### 2.1 Configure Log Forwarding

```bash
# Create rsyslog forwarding configuration
sudo tee /etc/rsyslog.d/10-forward.conf > /dev/null << 'EOF'
# Forward all logs to central server
*.* @@log-server:514  # TCP
*.* @log-server:514   # UDP

# Enable TLS forwarding
module(load="omtls")
action(type="omtls"
       target="log-server"
       port="6514"
       StreamDriver="gtls"
       StreamDriverMode="1"
       StreamDriverAuthMode="x509/name")
EOF
```

### 2.2 Set Up Log Generation

```bash
#!/bin/bash
# save as generate_logs.sh

# Generate various types of log messages
while true; do
    case $((RANDOM % 4)) in
        0) logger -p auth.info "User login from $(hostname) - success";;
        1) logger -p kern.warning "High CPU usage: $((50 + RANDOM % 50))%";;
        2) logger -p daemon.error "Database connection timeout";;
        3) logger -p syslog.info "Backup completed: $(date)";;
    esac
    sleep $((1 + RANDOM % 5))
done
```

## Testing and Verification

### 1. Security Tests

```bash
#!/bin/bash
# save as verify_security.sh

# Check permissions
echo "=== Checking Permissions ==="
find /var/log/clients -type f -ls

# Test log user access
echo "=== Testing loguser access ==="
sudo -u loguser touch /var/log/clients/test.log

# Verify log forwarding
echo "=== Testing log forwarding ==="
logger "Test message $(date)"
sleep 2
tail -n 1 /var/log/clients/catch-all.log
```

### 2. Performance Tests

```bash
#!/bin/bash
# save as test_performance.sh

# Generate high volume of logs
for i in {1..1000}; do
    logger "Test message $i" &
done

# Monitor system impact
sar 1 10
```

## Submission Requirements

Create a single ZIP file containing:

1. **Documentation**
   - Implementation notes
   - Test results
   - Security configurations
   - Dashboard screenshots

2. **Scripts**
   - All custom scripts
   - Configuration files
   - Test results

3. **System Information**
   ```bash
   # Generate system info
   {
       echo "=== System Information ==="
       uname -a
       echo -e "\n=== Network Configuration ==="
       ip addr show
       echo -e "\n=== Package Versions ==="
       dpkg -l | grep -E 'rsyslog|auditd|logrotate'
   } > system_info.txt
   ```

## Grading Criteria

| Component | Points | Requirements |
|-----------|--------|--------------|
| Setup | 20 | - Proper VM configuration<br>- Network connectivity<br>- Package installation |
| Security | 30 | - User/group setup<br>- Permissions<br>- TLS configuration |
| Implementation | 30 | - Log forwarding<br>- Dashboard<br>- Rotation |
| Documentation | 20 | - Clear documentation<br>- Test results<br>- Security audit |

## Troubleshooting Guide

1. **Common Issues**
   - Logs not forwarding: Check network/firewall
   - Permission denied: Verify loguser setup
   - High CPU usage: Check rotation settings

2. **Debug Commands**
   ```bash
   # Check rsyslog status
   sudo systemctl status rsyslog
   
   # View recent logs
   sudo journalctl -u rsyslog
   
   # Test network connectivity
   nc -vz log-server 514
   ```

## Security Best Practices

1. Use TLS for log transmission
2. Implement log rotation and compression
3. Monitor log directory permissions
4. Regular security audits
5. Backup critical logs

This improved guide provides:
- Clear separation of server/client tasks
- Enhanced security configurations
- Robust error handling
- Simplified submission process
- Comprehensive testing procedures
