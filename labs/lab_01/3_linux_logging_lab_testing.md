# Linux Logging Lab: Testing Guide
## Use this guide to verify your setup works correctly

### Quick Verification Script
Save this script as `quick_check.sh`:
```bash
#!/bin/bash
echo "=== Quick System Check ==="

# 1. Check if services are running
echo "1. Service Check:"
for service in rsyslog auditd; do
    systemctl is-active --quiet $service && 
    echo "✅ $service running" || 
    echo "❌ $service not running"
done

# 2. Check log directories
echo -e "\n2. Directory Check:"
ls -ld /var/log/clients/* 2>/dev/null && 
echo "✅ Log directories exist" || 
echo "❌ Log directories missing"

# 3. Check network
echo -e "\n3. Network Check:"
ping -c 1 192.168.100.10 &>/dev/null && 
echo "✅ Can reach server" || 
echo "❌ Cannot reach server"

# 4. Test log forwarding
echo -e "\n4. Log Forwarding Check:"
logger "TEST_$(date +%s)"
sleep 2
tail -n 1 /var/log/clients/app/catch-all.log 2>/dev/null && 
echo "✅ Log forwarding works" || 
echo "❌ Log forwarding failed"
```

### Expected Results

1. **User & Permissions**
```bash
# Run this:
id loguser

# Should see:
uid=999(loguser) gid=999(loggroup) groups=999(loggroup),4(adm),103(syslog)
```

2. **Directory Structure**
```bash
# Run this:
ls -la /var/log/clients/

# Should see:
drwxr-x--- 4 loguser loggroup 4096 Mar 18 14:30 .
drwxr-x--- 2 loguser loggroup 4096 Mar 18 14:30 auth
drwxr-x--- 2 loguser loggroup 4096 Mar 18 14:30 system
drwxr-x--- 2 loguser loggroup 4096 Mar 18 14:30 app
```

3. **Service Status**
```bash
# Run this:
systemctl status rsyslog

# Should see:
● rsyslog.service - System Logging Service
     Loaded: loaded
     Active: active (running)
```

### Common Problems & Solutions

1. **"Cannot create log file"**
   - Check permissions:
   ```bash
   sudo chown -R loguser:loggroup /var/log/clients
   sudo chmod -R 750 /var/log/clients
   ```

2. **"Logs not forwarding"**
   - Check network:
   ```bash
   # On client
   nc -vz 192.168.100.10 514
   ```
   - Check rsyslog:
   ```bash
   sudo systemctl restart rsyslog
   tail -f /var/log/syslog
   ```

3. **"Permission denied"**
   - Fix ownership:
   ```bash
   sudo chown loguser:loggroup /opt/monitoring/log_dashboard.sh
   sudo chmod 750 /opt/monitoring/log_dashboard.sh
   ```

### Full Test Suite
Run this after completing all lab steps:

```bash
cat << 'EOF' > full_test.sh
#!/bin/bash
echo "=== Full System Test ==="

# Function to check and report
check() {
    if [ $? -eq 0 ]; then
        echo "✅ $1"
    else
        echo "❌ $1"
        exit 1
    fi
}

# 1. Service Tests
systemctl is-active --quiet rsyslog
check "Rsyslog service"

# 2. User Tests
id loguser >/dev/null 2>&1
check "Logging user"

# 3. Permission Tests
sudo -u loguser touch /var/log/clients/test
check "Permissions"

# 4. Log Generation Test
logger "TEST_MESSAGE"
sleep 2
grep "TEST_MESSAGE" /var/log/clients/app/catch-all.log
check "Log forwarding"

echo "=== Test Complete ==="
EOF

chmod +x full_test.sh
```

### What Good Results Look Like

1. **Log Files**:
```
# Good log entry format:
Mar 18 14:30:45 hostname service[123]: message
```

2. **Permissions**:
```
# Correct directory permissions:
drwxr-x--- (750)

# Correct file permissions:
-rw-r----- (640)
```

3. **Network**:
```
# Good ping result:
64 bytes from 192.168.100.10: icmp_seq=1 ttl=64 time=0.123 ms
```

### Help & Debug Commands

1. **Check logs in real-time**:
```bash
# On server
tail -f /var/log/clients/app/catch-all.log

# On client
tail -f /var/log/syslog
```

2. **Check network**:
```bash
# View network config
ip addr show

# Test connectivity
ping -c 3 192.168.100.10
```

3. **View service status**:
```bash
# Check all related services
systemctl status rsyslog
journalctl -u rsyslog -n 50
```

Remember: If any test fails, fix it before moving to the next section of the lab.