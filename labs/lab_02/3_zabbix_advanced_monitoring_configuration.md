# Zabbix Advanced Monitoring Configuration

## 1. Web Scenario Creation

### Set Up Basic Monitoring
1. Navigate to: Configuration → Hosts → [Your Host] → Web
2. Create web scenario:
```plaintext
Name: Website Availability Check
Update interval: 1m
Attempts: 2

Steps:
1. Homepage Check
   - Name: Home
   - URL: http://[VM2-IP]:8081
   - Required string: "Status: OK"
   - Timeout: 15s
   
2. User Journey Check
   - Name: Journey
   - URL: http://[VM2-IP]:8082
   - Required string: "User Journey"
   - Timeout: 15s
```

### Advanced Monitoring Parameters
```plaintext
Add variables:
- {host.ip}
- {$WEBSITE_PORT}

Authentication:
- HTTP authentication: None
- Variables: Add if needed

Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
```

## 2. Custom Triggers

### Response Time Triggers
```plaintext
1. High Response Time
Expression: {TestWeb:web.test.time[Website Availability Check,Home,resp].last()}>3

2. Multiple Failed Attempts
Expression: {TestWeb:web.test.fail[Website Availability Check].min(#3)}>0

3. Status Changed
Expression: {TestWeb:web.test.error[Website Availability Check].diff()}=1
```

### Website Content Triggers
```plaintext
1. Status Error Detection
Expression: {TestWeb:web.test.regexp[Website Availability Check,Home,Status: Error].last()}=1

2. Journey Completion Monitor
Expression: {TestWeb:web.test.regexp[Website Availability Check,Journey,Order completed].last()}=0
```

## 3. Performance Testing

### 1. Install Apache Bench
```bash
# On VM1 (for testing)
sudo apt install apache2-utils
```

### 2. Basic Load Testing
```bash
# Test Homepage (Site 1)
ab -n 1000 -c 10 http://[VM2-IP]:8081/

# Test User Journey (Site 2)
ab -n 500 -c 5 http://[VM2-IP]:8082/
```

### 3. Advanced Performance Tests
```bash
# Sustained load test
ab -n 10000 -c 20 -t 300 http://[VM2-IP]:8081/

# Spike test
ab -n 5000 -c 100 http://[VM2-IP]:8081/

# Test with different keep-alive settings
ab -n 1000 -c 10 -k http://[VM2-IP]:8081/
```

## 4. Custom Dashboards

### Create Main Dashboard
1. Go to: Monitoring → Dashboards → Create dashboard
2. Add the following widgets:

### 1. Problems Overview
```plaintext
Type: Problems
Name: Current Website Issues
Show:
- Host groups: Linux servers
- Problem: All
- Severity: All
```

### 2. Response Time Graph
```plaintext
Type: Graph (classic)
Name: Website Response Times
- Add: web.test.time[*] metrics
- Time period: Last hour
```

### 3. Availability Report
```plaintext
Type: Web monitoring
Hosts: Select your test website
Show:
- Response time
- Response code
- Failed steps
```

## 5. Notifications Setup

### 1. Create Media Type
```plaintext
Administration → Media types → Create media type
- Name: Website Alert
- Type: Email
```

### 2. Configure Action
```plaintext
Configuration → Actions → Create action
Conditions:
- Type: Trigger
- Trigger name: "Website Availability Check"
Operations:
- Send message to specified group
```

## 6. Advanced Agent Configuration

### 1. Custom Parameters
```bash
# On VM2, edit agent configuration
sudo nano /etc/zabbix/zabbix_agentd.d/web_monitoring.conf

# Add custom parameters
UserParameter=custom.web.response[*],curl -o /dev/null -s -w "%{time_total}" $1
UserParameter=custom.web.size[*],curl -sI $1 | grep -i content-length | awk '{print $2}'
UserParameter=custom.web.status[*],curl -o /dev/null -s -w "%{http_code}" $1
```

### 2. Test Parameters
```bash
# On VM1, test the parameters
zabbix_get -s [VM2-IP] -k custom.web.response[http://localhost:8081]
zabbix_get -s [VM2-IP] -k custom.web.status[http://localhost:8081]
```

## 7. NGINX Integration

### 1. Configure NGINX Status
```bash
# On VM2, edit NGINX configuration
sudo nano /etc/nginx/sites-available/default

# Add status location
location /nginx_status {
    stub_status on;
    allow 127.0.0.1;
    allow [VM1-IP];
    deny all;
}
```

### 2. Add NGINX Monitoring
```bash
# Add UserParameters for NGINX
UserParameter=nginx.connections.active,curl -s http://localhost/nginx_status | grep 'Active' | awk '{print $3}'
UserParameter=nginx.connections.reading,curl -s http://localhost/nginx_status | grep 'Reading' | awk '{print $2}'
UserParameter=nginx.connections.writing,curl -s http://localhost/nginx_status | grep 'Writing' | awk '{print $4}'
UserParameter=nginx.connections.waiting,curl -s http://localhost/nginx_status | grep 'Waiting' | awk '{print $6}'
```

## 8. Testing Scenarios

### 1. Availability Test
```bash
# Stop NGINX
sudo systemctl stop nginx

# Check Zabbix for alerts
# Start NGINX
sudo systemctl start nginx
```

### 2. Performance Test
```bash
# Create load
for i in {1..100}; do
    curl http://[VM2-IP]:8081/ &
done

# Check response times in Zabbix
```

### 3. Content Test
```bash
# Modify website content
sudo sed -i 's/Status: OK/Status: Error/' /var/www/html/site1/index.html

# Check trigger activation
```

## 9. Regular Maintenance

### 1. Log Rotation
```bash
# Configure logrotate
sudo nano /etc/logrotate.d/nginx-monitoring

/var/log/nginx/*log {
    daily
    missingok
    rotate 14
    compress
    delaycompress
    notifempty
    create 0640 www-data adm
    sharedscripts
    prerotate
        if [ -d /etc/logrotate.d/httpd-prerotate ]; then \
            run-parts /etc/logrotate.d/httpd-prerotate; \
        fi \
    endscript
    postrotate
        invoke-rc.d nginx rotate >/dev/null 2>&1
    endscript
}
```

### 2. Regular Checks
```bash
# Check NGINX configuration
sudo nginx -t

# Check Zabbix agent status
sudo systemctl status zabbix-agent

# Verify logs
sudo tail -f /var/log/zabbix/zabbix_agentd.log
```

## 10. Documentation Requirements

### Screenshot Checklist:
1. Web Scenario Configuration
2. Trigger Settings
3. Dashboard Overview
4. Performance Test Results
5. NGINX Status Page
6. Alert Configuration
7. Custom Item Configuration

Remember to mask any sensitive information in screenshots!