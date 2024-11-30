# Linux Logging Lab - Advanced Features
## After completing basic setup, continue here

### 1. Log Categories Setup (30 minutes)
On VM1 (Log Server):
```bash
# Create category directories
sudo mkdir -p /var/log/remote/{auth,system,app}

# Create sorting configuration
sudo nano /etc/rsyslog.d/categories.conf

# Add these rules:
auth.* /var/log/remote/auth/auth.log
kern.* /var/log/remote/system/kern.log
*.* /var/log/remote/app/catch-all.log

# Restart rsyslog
sudo systemctl restart rsyslog
```

### 2. Log Rotation Setup (30 minutes)
```bash
# Create rotation configuration
sudo nano /etc/logrotate.d/remote-logs

# Add this configuration:
/var/log/remote/*/*.log {
    daily
    rotate 7
    compress
    delaycompress
    missingok
    notifempty
    create 644 syslog adm
    postrotate
        systemctl reload rsyslog
    endscript
}
```

### 3. Logging Dashboard (45 minutes)

1. Install required packages:
```bash
sudo apt install -y gawk bc sysstat net-tools
```

2. Create dashboard script:
```bash
cat << 'EOF' > /opt/log_dashboard.sh
#!/bin/bash
while true; do
    clear
    echo "=== Log Analysis Dashboard ==="
    echo "Last Update: $(date)"
    echo "------------------------"
    
    # Count errors and warnings
    echo "Last 5 minutes:"
    echo "Errors: $(grep -c ERROR /var/log/remote/app/catch-all.log)"
    echo "Warnings: $(grep -c WARNING /var/log/remote/app/catch-all.log)"
    
    # Show disk usage
    echo "------------------------"
    echo "Log Storage:"
    df -h /var/log/remote
    
    sleep 10
done
EOF

chmod +x /opt/log_dashboard.sh
```

### 4. Log Generation for Testing (15 minutes)

Create test log generator on VM2:
```bash
cat << 'EOF' > ~/test_logs.sh
#!/bin/bash
while true; do
    # Generate random log types
    case $((RANDOM % 3)) in
        0) logger -p auth.info "User login" ;;
        1) logger -p kern.warning "High CPU usage" ;;
        2) logger -p daemon.error "Service error" ;;
    esac
    sleep 5
done
EOF

chmod +x ~/test_logs.sh
```

### 5. Security Features (30 minutes)

1. Install audit system:
```bash
sudo apt install auditd

# Add basic rules
sudo auditctl -w /var/log/remote -p wa -k log_changes
```

2. Create security report:
```bash
cat << 'EOF' > ~/security_check.sh
#!/bin/bash
echo "=== Security Check ==="
echo "Log File Permissions:"
ls -l /var/log/remote/*

echo -e "\nAudit Log Events:"
sudo ausearch -k log_changes -ts recent
EOF

chmod +x ~/security_check.sh
```

### 6. Verification Steps

Test each component:
```bash
# 1. Log Categories
logger -p auth.info "Auth test"
logger -p kern.warning "Kernel test"
logger -p daemon.error "Error test"

# 2. Log Rotation
sudo logrotate -f /etc/logrotate.d/remote-logs

# 3. Dashboard
/opt/log_dashboard.sh

# 4. Security
./security_check.sh
```

## Common Advanced Issues

1. **Log Rotation Not Working**
   ```bash
   # Test rotation manually
   sudo logrotate -d /etc/logrotate.d/remote-logs
   ```

2. **Dashboard Performance Issues**
   ```bash
   # Use less frequent updates
   # Modify sleep value in dashboard script
   ```

3. **Security Audit Issues**
   ```bash
   # Check audit service
   sudo systemctl status auditd
   ```

## Bonus Tasks

1. Add email alerts for errors:
```bash
# Install mailutils
sudo apt install mailutils

# Add to rsyslog.conf:
*.err action(type="ommail" server="localhost" port="25")
```

2. Create weekly report:
```bash
cat << 'EOF' > ~/weekly_report.sh
#!/bin/bash
echo "Log Summary for $(date)"
echo "Total Errors: $(grep -c ERROR /var/log/remote/app/catch-all.log)"
echo "Total Warnings: $(grep -c WARNING /var/log/remote/app/catch-all.log)"
EOF
```

## Final Test Checklist

- [ ] Logs are properly categorized
- [ ] Log rotation works
- [ ] Dashboard shows real-time data
- [ ] Security monitoring active
- [ ] All scripts are executable
- [ ] Permissions are correct

## Need Help?

1. Check rsyslog logs:
```bash
sudo journalctl -u rsyslog
```

2. Verify file permissions:
```bash
ls -lR /var/log/remote
```

3. Test log forwarding:
```bash
logger "Test message"
tail -f /var/log/remote/app/catch-all.log
```