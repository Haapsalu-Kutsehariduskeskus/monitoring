# Testing Guide and Expected Results

## 1. Initial Setup Verification

### Test User and Permissions
```bash
# Command
id loguser

# Expected Output
uid=999(loguser) gid=999(loggroup) groups=999(loggroup),4(adm),103(syslog)

# Command
ls -la /opt/monitoring/
# Expected Output
drwxr-x--- 3 loguser loggroup 4096 Mar 18 14:30 .
drwxr-x--- 2 loguser loggroup 4096 Mar 18 14:30 scripts
-rwxr-x--- 1 loguser loggroup 2345 Mar 18 14:30 log_dashboard.sh

# Command
ls -la /var/log/clients/
# Expected Output
drwxr-x--- 4 loguser loggroup 4096 Mar 18 14:30 .
drwxr-x--- 2 loguser loggroup 4096 Mar 18 14:30 auth
drwxr-x--- 2 loguser loggroup 4096 Mar 18 14:30 system
drwxr-x--- 2 loguser loggroup 4096 Mar 18 14:30 app
```

## 2. Service Status Verification

### On VM1 (Log Server)
```bash
# Command
sudo systemctl status log-dashboard
# Expected Output
● log-dashboard.service - Log Analysis Dashboard
     Loaded: loaded (/etc/systemd/system/log-dashboard.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2024-03-18 14:30:00 UTC; 1min ago
   Main PID: 1234 (log_dashboard.sh)
      Tasks: 2 (limit: 1147)
     Memory: 6.8M
        CPU: 89ms
     CGroup: /system.slice/log-dashboard.service
             └─1234 /bin/bash /opt/monitoring/log_dashboard.sh

# Command
sudo journalctl -u log-dashboard -n 10
# Expected Output
Mar 18 14:30:00 logserver systemd[1]: Started Log Analysis Dashboard.
Mar 18 14:30:01 logserver log_dashboard.sh[1234]: === Log Analysis Dashboard ===
Mar 18 14:30:01 logserver log_dashboard.sh[1234]: Updated: 2024-03-18 14:30:01
[Additional log entries showing regular updates]
```

### On VM2 (Client)
```bash
# Command
sudo systemctl status log-generator
# Expected Output
● log-generator.service - Test Log Generator
     Loaded: loaded (/etc/systemd/system/log-generator.service; enabled; vendor preset: enabled)
     Active: active (running) since Thu 2024-03-18 14:30:00 UTC; 1min ago
   Main PID: 1235 (generate_logs.sh)
      Tasks: 2 (limit: 1147)
     Memory: 4.2M
        CPU: 45ms
     CGroup: /system.slice/log-generator.service
             └─1235 /bin/bash /opt/monitoring/scripts/generate_logs.sh

# Command
sudo journalctl -u log-generator -n 10
# Expected Output
[Log entries showing regular test message generation]
```

## 3. Log Generation and Forwarding Test

### On VM2 (Client)
```bash
# Command
logger "TEST_MESSAGE_$(date +%s)"
echo $?
# Expected Output
0

# Command
tail -f /var/log/syslog | grep TEST_MESSAGE
# Expected Output
Mar 18 14:30:10 logclient logger: TEST_MESSAGE_1710766210
```

### On VM1 (Server)
```bash
# Command
tail -f /var/log/clients/catch-all.log | grep TEST_MESSAGE
# Expected Output
Mar 18 14:30:10 logclient logger: TEST_MESSAGE_1710766210
```

## 4. Dashboard Functionality Test

### Check Dashboard Output
```bash
# Connect to VM1 and run:
sudo tail -f /var/log/journal/*/system.journal | grep "log_dashboard"

# Expected Output Format:
=== Log Analysis Dashboard ===
Updated: 2024-03-18 14:30:45
--------------------------------
Errors (last 5 min): X
Warnings (last 5 min): Y
Info (last 5 min): Z
--------------------------------
Authentication Failures: A
Error Rate: B%
--------------------------------
Storage Status:
Used: C% of DG (EG used, FG free)
```

## 5. End-to-End Testing Script

```bash
#!/bin/bash
# save as test_all.sh

echo "=== Starting Complete System Test ==="

# 1. Check Services
echo "Checking services..."
services=("rsyslog" "log-dashboard" "log-generator")
for service in "${services[@]}"; do
    if systemctl is-active --quiet $service; then
        echo "✓ $service is running"
    else
        echo "✗ $service is NOT running"
        exit 1
    fi
done

# 2. Test Log Generation
echo "Testing log generation..."
TEST_ID=$(date +%s)
logger "TEST_MESSAGE_$TEST_ID"
sleep 2

# 3. Check Log Reception
echo "Checking log reception..."
if grep -q "TEST_MESSAGE_$TEST_ID" /var/log/clients/catch-all.log; then
    echo "✓ Log forwarding works"
else
    echo "✗ Log forwarding failed"
    exit 1
fi

# 4. Check Dashboard
echo "Checking dashboard output..."
if pgrep -f "log_dashboard.sh" > /dev/null; then
    echo "✓ Dashboard is running"
else
    echo "✗ Dashboard is not running"
    exit 1
fi

# 5. Check Permissions
echo "Checking permissions..."
if sudo -u loguser touch /var/log/clients/test_file && \
   rm /var/log/clients/test_file; then
    echo "✓ Permissions are correct"
else
    echo "✗ Permission issues detected"
    exit 1
fi

echo "=== Test Complete ==="
```

## Running Complete System Test
```bash
sudo bash test_all.sh

# Expected Output
=== Starting Complete System Test ===
Checking services...
✓ rsyslog is running
✓ log-dashboard is running
✓ log-generator is running
Testing log generation...
Checking log reception...
✓ Log forwarding works
Checking dashboard output...
✓ Dashboard is running
Checking permissions...
✓ Permissions are correct
=== Test Complete ===
```

## Troubleshooting Common Issues

1. **If Log Forwarding Fails:**
```bash
# Check network connectivity
nc -vz vm1_ip 514
# Expected: Connection successful

# Check rsyslog configuration
sudo rsyslogd -N1
# Expected: rsyslogd: version X.X.X, config validation run (level 1), master config /etc/rsyslog.conf
# rsyslogd: End of config validation run. Bye.
```

2. **If Dashboard Not Updating:**
```bash
# Check log file permissions
sudo -u loguser ls -l /var/log/clients/
# Should show read access for all files

# Check dashboard process
ps aux | grep log_dashboard
# Should show running process
```

3. **If Services Won't Start:**
```bash
# Check service errors
sudo journalctl -u service_name -n 50
# Look for specific error messages

# Verify script permissions
ls -l /opt/monitoring/
# All scripts should be executable (750)
```

Use this guide to verify that all components are working correctly. If any test fails, refer to the troubleshooting section for the specific component.