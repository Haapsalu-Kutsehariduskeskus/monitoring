# VSCode Setup Guide for Linux Lab Work

## 1. Basic Setup

### Installing VSCode
```bash
# For Ubuntu/Debian
sudo apt update
sudo apt install wget gpg
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -D -o root -g root -m 644 packages.microsoft.gpg /usr/share/keyrings/packages.microsoft.gpg
sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/usr/share/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
sudo apt update
sudo apt install code
```

## 2. Essential Extensions for Lab Work

### 1. Remote Development Pack
- **Name**: Remote Development
- **Purpose**: Connect to VMs via SSH
- **Installation**: Search "Remote Development" in Extensions
- **Setup**:
  ```bash
  # On your VMs, ensure SSH is installed
  sudo apt install openssh-server
  
  # Get VM IP
  ip addr show
  
  # In VSCode:
  # 1. Press F1
  # 2. Type "Remote-SSH: Connect to Host"
  # 3. Enter: ssh student@<VM-IP>
  ```

### 2. Markdown Support
- **Name**: Markdown All in One
- **Purpose**: Edit lab documentation
- **Features**:
  - Live preview
  - Table of contents
  - Auto-formatting

### 3. Mermaid Support
- **Name**: Markdown Preview Mermaid Support
- **Purpose**: View and edit Mermaid diagrams
- **Usage**:
  - Open .md file
  - Press Ctrl+Shift+V for preview
  - Diagrams render automatically

### 4. Log File Viewer
- **Name**: Log File Highlighter
- **Purpose**: Better log file reading
- **Supports**:
  - syslog
  - rsyslog
  - audit logs

### 5. YAML/Configuration Support
- **Name**: YAML
- **Purpose**: Edit configuration files
- **Features**:
  - Syntax highlighting
  - Schema validation
  - Auto-formatting

## 3. VirtualBox Integration

### Setting Up SSH Access
1. Configure VirtualBox Network:
   ```bash
   # In VirtualBox Manager:
   # 1. Select VM -> Settings -> Network
   # 2. Adapter 1: NAT
   # 3. Advanced -> Port Forwarding
   # Add rule:
   # Name: SSH
   # Protocol: TCP
   # Host Port: 2222
   # Guest Port: 22
   ```

2. Configure SSH Config:
   ```bash
   # In your home directory
   mkdir -p ~/.ssh
   nano ~/.ssh/config
   
   # Add:
   Host vm1
       HostName localhost
       Port 2222
       User student
   
   Host vm2
       HostName localhost
       Port 2223
       User student
   ```

### VSCode Workspace Setup
1. Create workspace file:
   ```json
   {
       "folders": [
           {
               "name": "VM1 - Log Server",
               "uri": "vscode-remote://ssh-remote+vm1/home/student/lab_01"
           },
           {
               "name": "VM2 - Client",
               "uri": "vscode-remote://ssh-remote+vm2/home/student/lab_01"
           }
       ],
       "settings": {
           "files.associations": {
               "*.conf": "properties",
               "*.rules": "properties"
           },
           "terminal.integrated.defaultProfile.linux": "bash"
       }
   }
   ```

## 4. Useful VSCode Features for Lab Work

### 1. Split Terminal
```bash
# Use multiple terminals:
# 1. Ctrl + Shift + ` (new terminal)
# 2. Ctrl + Shift + 5 (split terminal)
# Monitor both VMs simultaneously
```

### 2. Git Integration
```bash
# Initialize repository
git init

# Stage changes
git add .

# Commit
git commit -m "Initial lab setup"

# Push to GitHub (for submission)
git remote add origin <your-repo-url>
git push -u origin main
```

### 3. Live Share (Optional)
- Install "Live Share" extension
- Share your workspace with instructor for help
- Real-time collaboration

### 4. Custom Tasks
Create `.vscode/tasks.json`:
```json
{
    "version": "2.0.0",
    "tasks": [
        {
            "label": "Monitor Logs",
            "type": "shell",
            "command": "tail -f /var/log/clients/app/catch-all.log",
            "presentation": {
                "reveal": "always",
                "panel": "new"
            }
        },
        {
            "label": "Check Services",
            "type": "shell",
            "command": "systemctl status rsyslog auditd",
            "presentation": {
                "reveal": "always",
                "panel": "new"
            }
        }
    ]
}
```

### 5. Keyboard Shortcuts
Essential shortcuts for lab work:
- `Ctrl+` ` : Terminal
- `Ctrl+Shift+E`: File Explorer
- `Ctrl+P`: Quick Open File
- `Ctrl+Shift+F`: Search in Files
- `F1`: Command Palette

## 5. Troubleshooting Common Issues

### SSH Connection Issues
```bash
# Check SSH service
sudo systemctl status ssh

# Check port forwarding
sudo netstat -tulpn | grep ssh

# Test connection
ssh -vv student@localhost -p 2222
```

### Permission Issues
```bash
# If you can't access logs
sudo usermod -aG adm,syslog $USER
newgrp adm

# If you can't edit config files
sudo chown -R $USER:$USER ~/.vscode-server
```

### Remote Development Issues
1. Check SSH key setup:
   ```bash
   ssh-keygen -t rsa -b 4096
   ssh-copy-id student@<VM-IP>
   ```

2. Check VSCode server:
   ```bash
   # On VM
   ps aux | grep vscode-server
   ```

## 6. Best Practices

1. **Workspace Organization**
   - Keep separate folders for each VM
   - Use meaningful file names
   - Keep configurations in version control

2. **Terminal Management**
   - Use split terminals for monitoring
   - Name your terminals (right-click → Rename)
   - Save common commands in tasks.json

3. **Git Integration**
   - Commit frequently
   - Use meaningful commit messages
   - Keep sensitive data out of git

4. **Remote Work**
   - Use SSH keys for authentication
   - Keep backup connection method ready
   - Monitor resource usage