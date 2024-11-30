# Test Website Setup for Zabbix Monitoring

## Basic Website Setup (VM2)

### 1. Install NGINX
```bash
# Install NGINX web server
sudo apt install -y nginx

# Verify installation
sudo systemctl status nginx
```

### 2. Create Test Websites

#### Website 1: Status Monitor
```bash
# Create directory for first site
sudo mkdir -p /var/www/html/site1

# Create index file
sudo nano /var/www/html/site1/index.html
```

Add this content:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Test Site 1 - Status Monitor</title>
    <style>
        body { 
            font-family: Arial, sans-serif; 
            margin: 40px;
            background: #f4f4f4;
        }
        .container {
            background: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        .status { 
            padding: 20px; 
            margin: 20px 0; 
            border-radius: 4px;
        }
        .ok { 
            background: #dff0d8; 
            color: #3c763d;
        }
        .error { 
            background: #f2dede; 
            color: #a94442;
        }
        button {
            padding: 10px 20px;
            background: #337ab7;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }
        button:hover {
            background: #286090;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Test Site 1 - Status Monitor</h1>
        <div class="status ok" id="statusBox">
            Status: OK
        </div>
        <div>
            <p>Server Time: <span id="currentTime"></span></p>
            <p>Uptime: <span id="uptime">0</span> seconds</p>
            <button onclick="toggleStatus()">Toggle Status</button>
        </div>
    </div>

    <script>
        let startTime = new Date();
        
        function updateTime() {
            document.getElementById('currentTime').textContent = 
                new Date().toLocaleString();
            
            let uptime = Math.floor((new Date() - startTime) / 1000);
            document.getElementById('uptime').textContent = uptime;
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

#### Website 2: User Journey Simulator
```bash
# Create directory for second site
sudo mkdir -p /var/www/html/site2

# Create index file
sudo nano /var/www/html/site2/index.html
```

Add this content:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Test Site 2 - User Journey</title>
    <style>
        body { 
            font-family: Arial, sans-serif; 
            margin: 40px;
            background: #f4f4f4;
        }
        .container {
            background: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        .form-group { 
            margin: 20px 0; 
        }
        .step { 
            padding: 20px; 
            margin: 10px 0; 
            background: #f8f9fa;
            border-radius: 4px;
        }
        input, select {
            padding: 8px;
            margin: 5px 0;
            border: 1px solid #ddd;
            border-radius: 4px;
            width: 200px;
        }
        button {
            padding: 10px 20px;
            background: #28a745;
            color: white;
            border: none;
            border-radius: 4px;
            cursor: pointer;
        }
        button:hover {
            background: #218838;
        }
        label {
            display: block;
            margin-bottom: 5px;
        }
    </style>
</head>
<body>
    <div class="container">
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
                    <option value="1">Basic Monitor ($100)</option>
                    <option value="2">Pro Monitor ($200)</option>
                    <option value="3">Enterprise Monitor ($500)</option>
                </select>
            </div>
            <button onclick="nextStep(3)">Add to Cart</button>
        </div>

        <div class="step" id="step3" style="display:none;">
            <h2>Step 3: Checkout</h2>
            <div id="orderSummary"></div>
            <div id="loadTime"></div>
            <button onclick="complete()">Complete Order</button>
        </div>
    </div>

    <script>
        const startTime = new Date();
        
        function nextStep(step) {
            document.querySelectorAll('.step').forEach(el => el.style.display = 'none');
            document.getElementById('step' + step).style.display = 'block';
            
            if(step === 3) {
                const product = document.getElementById('product');
                const summary = document.getElementById('orderSummary');
                const loadTime = document.getElementById('loadTime');
                
                summary.innerHTML = `
                    <p><strong>Order Summary:</strong></p>
                    <p>Selected: ${product.options[product.selectedIndex].text}</p>
                    <p>User: ${document.getElementById('username').value}</p>
                `;
                
                const timeElapsed = (new Date() - startTime) / 1000;
                loadTime.innerHTML = `<p>Journey Time: ${timeElapsed.toFixed(2)} seconds</p>`;
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

### 3. Configure NGINX Virtual Hosts

#### Site 1 Configuration
```bash
# Create configuration for site1
sudo nano /etc/nginx/sites-available/site1

# Add this configuration:
server {
    listen 8081;
    server_name _;
    
    access_log /var/log/nginx/site1-access.log;
    error_log /var/log/nginx/site1-error.log;

    root /var/www/html/site1;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

#### Site 2 Configuration
```bash
# Create configuration for site2
sudo nano /etc/nginx/sites-available/site2

# Add this configuration:
server {
    listen 8082;
    server_name _;
    
    access_log /var/log/nginx/site2-access.log;
    error_log /var/log/nginx/site2-error.log;

    root /var/www/html/site2;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

### 4. Enable Sites
```bash
# Create symbolic links
sudo ln -s /etc/nginx/sites-available/site1 /etc/nginx/sites-enabled/
sudo ln -s /etc/nginx/sites-available/site2 /etc/nginx/sites-enabled/

# Test configuration
sudo nginx -t

# If test is successful, restart NGINX
sudo systemctl restart nginx
```

### 5. Configure Firewall
```bash
# Allow NGINX and test site ports
sudo ufw allow 80/tcp
sudo ufw allow 8081/tcp
sudo ufw allow 8082/tcp
```

### 6. Test Access
```bash
# Test from command line
curl http://localhost:8081
curl http://localhost:8082

# Access from browser:
http://[VM2-IP]:8081
http://[VM2-IP]:8082
```

## Configure Zabbix Monitoring (VM1)

### 1. Add Web Monitoring

1. Go to: Configuration → Hosts → TestWeb-YourName → Web
2. Click "Create web scenario"

#### For Site 1:
- Name: Status Monitor
- Update interval: 30s
- Steps:
  1. Homepage Check
     - Name: Check Status
     - URL: http://[VM2-IP]:8081
     - Required status codes: 200
     - Required string: Status: OK

#### For Site 2:
- Name: User Journey
- Update interval: 1m
- Steps:
  1. Login Page
     - Name: Login Page
     - URL: http://[VM2-IP]:8082
     - Required status codes: 200
     - Required string: Login
  2. Product Selection
     - Name: Products Page
     - URL: http://[VM2-IP]:8082
     - Required string: Product Selection

### 2. Add Triggers

1. Go to: Configuration → Hosts → TestWeb-YourName → Triggers
2. Create trigger:
   - Name: Website response time too high
   - Expression: {TestWeb-YourName:web.test.time[Status Monitor,Check Status,resp].avg(5m)}>3

### 3. Create Dashboard

1. Go to: Monitoring → Dashboards → Create dashboard
2. Add widgets:
   - Problems widget (show website issues)
   - Web monitoring widget (show response times)
   - Graph widget (show performance trends)

## Troubleshooting

### Common Issues:
1. Cannot access websites:
```bash
# Check NGINX status
sudo systemctl status nginx

# Check NGINX logs
sudo tail -f /var/log/nginx/error.log
sudo tail -f /var/log/nginx/site1-error.log
sudo tail -f /var/log/nginx/site2-error.log
```

2. Permission issues:
```bash
# Fix permissions
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```

3. Port conflicts:
```bash
# Check if ports are in use
sudo netstat -tulpn | grep -E ':(80|8081|8082)'
```

### Testing Tools:
```bash
# Test website response
curl -I http://localhost:8081
curl -I http://localhost:8082

# Monitor access logs
sudo tail -f /var/log/nginx/site1-access.log
sudo tail -f /var/log/nginx/site2-access.log
```