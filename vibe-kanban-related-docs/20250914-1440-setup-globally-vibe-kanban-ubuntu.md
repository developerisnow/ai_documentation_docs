---
created: 2025-09-14 14:40
type: setup-guide
sphere: development
topic: vibe-kanban
tags: [vibe-kanban, mcp, ubuntu, global-setup, claude-code, eywa1]
---

# Global Vibe-Kanban Setup for Ubuntu (eywa1)

## <¯ Key Insights

### Current eywa1 Status
- **Note from user**: Global vibe-kanban likely already running on port 5170
- **Need to verify**: Check if service is active before setting up

### Architecture Notes
- Same as macOS: One global instance serving all projects
- Config in `~/.claude.json` for Claude Code
- SQLite database in working directory

## =Ë Setup Instructions for Ubuntu (eywa1)

### Step 1: Check Existing Setup
```bash
# SSH to eywa1
ssh eywa1

# Check if vibe-kanban already running
ps aux | grep vibe-kanban
lsof -i :5170

# Check systemd services
systemctl --user list-units | grep vibe
systemctl list-units | grep vibe

# Check if already configured
cat ~/.claude.json | jq '.mcpServers["vibe-kanban"]'
```

### Step 2: If NOT Already Running - Install

#### Prerequisites
```bash
# Ensure Node.js is available
source ~/.nvm/nvm.sh
nvm use 20

# Test npx availability
which npx
# Should be: /home/user/.nvm/versions/node/v20.19.5/bin/npx
```

#### Configure Global MCP
```bash
# Edit ~/.claude.json
nano ~/.claude.json
```

Add configuration:
```json
{
  "mcpServers": {
    "vibe-kanban": {
      "command": "/home/user/.nvm/versions/node/v20.19.5/bin/npx",
      "args": ["-y", "vibe-kanban", "--mcp"]
    }
  }
}
```

### Step 3: Create Systemd Service

#### User Service (Recommended)
```bash
# Create user service directory if not exists
mkdir -p ~/.config/systemd/user/

# Create service file
cat > ~/.config/systemd/user/vibe-kanban.service << 'EOF'
[Unit]
Description=Vibe-Kanban Global Service
After=network.target

[Service]
Type=simple
Environment="PORT=5170"
Environment="PATH=/home/user/.nvm/versions/node/v20.19.5/bin:/usr/local/bin:/usr/bin:/bin"
WorkingDirectory=/home/user/vibe-kanban
ExecStart=/home/user/.nvm/versions/node/v20.19.5/bin/npx -y vibe-kanban
Restart=always
RestartSec=10
StandardOutput=append:/home/user/vibe-kanban.log
StandardError=append:/home/user/vibe-kanban-error.log

[Install]
WantedBy=default.target
EOF

# Create working directory
mkdir -p ~/vibe-kanban

# Reload systemd
systemctl --user daemon-reload

# Enable and start service
systemctl --user enable vibe-kanban
systemctl --user start vibe-kanban

# Check status
systemctl --user status vibe-kanban
```

### Step 4: Alternative - Screen/Tmux Session
```bash
# Using screen (persistent session)
screen -S vibe-kanban
export PORT=5170
/home/user/.nvm/versions/node/v20.19.5/bin/npx -y vibe-kanban
# Detach: Ctrl+A, D

# Reattach later
screen -r vibe-kanban

# Using tmux
tmux new -s vibe-kanban
export PORT=5170
/home/user/.nvm/versions/node/v20.19.5/bin/npx -y vibe-kanban
# Detach: Ctrl+B, D

# Reattach
tmux attach -t vibe-kanban
```

### Step 5: Verify Installation
```bash
# Check if running
curl http://localhost:5170

# Check logs
tail -f ~/vibe-kanban.log

# Test from remote (your Mac)
ssh -L 5170:localhost:5170 eywa1
# Then browse http://localhost:5170 on Mac
```

## =' Service Management

### Start/Stop/Restart
```bash
# User service
systemctl --user start vibe-kanban
systemctl --user stop vibe-kanban
systemctl --user restart vibe-kanban

# View logs
journalctl --user -u vibe-kanban -f

# Check if port is listening
ss -tulpn | grep 5170
```

### Auto-start Configuration
```bash
# Enable lingering (services run without SSH session)
sudo loginctl enable-linger $USER

# Verify
loginctl show-user $USER | grep Linger
```

## < Remote Access Configuration

### Option A: SSH Tunnel (Development)
```bash
# From Mac, tunnel to eywa1
ssh -L 5170:localhost:5170 eywa1

# Access at http://localhost:5170
```

### Option B: Nginx Reverse Proxy (Production)
```bash
# Install nginx if needed
sudo apt update
sudo apt install nginx

# Create config
sudo nano /etc/nginx/sites-available/vibe-kanban

# Add configuration
server {
    listen 80;
    server_name vibe.eywa1.local;

    location / {
        proxy_pass http://127.0.0.1:5170;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

# Enable site
sudo ln -s /etc/nginx/sites-available/vibe-kanban /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

## = Troubleshooting

### Port Already in Use
```bash
# Find process
sudo lsof -i :5170
sudo netstat -tulpn | grep 5170

# Kill if needed
kill -9 $(lsof -t -i:5170)
```

### Service Won't Start
```bash
# Check logs
journalctl --user -u vibe-kanban -n 50

# Manual test
export PORT=5170
/home/user/.nvm/versions/node/v20.19.5/bin/npx -y vibe-kanban
```

### Database Issues
```bash
# Find database
find ~ -name "db.sqlite" 2>/dev/null | grep vibe

# Backup database
cp ~/vibe-kanban/db.sqlite ~/vibe-kanban/db.sqlite.backup

# Reset if corrupted
rm ~/vibe-kanban/db.sqlite
systemctl --user restart vibe-kanban
```

## =Ê Multi-User Setup (Optional)

If multiple users need access on eywa1:
```bash
# System-wide service (requires sudo)
sudo nano /etc/systemd/system/vibe-kanban.service

# Configure for all users
[Service]
User=vibe-kanban
Group=vibe-kanban
WorkingDirectory=/var/lib/vibe-kanban

# Create system user
sudo useradd -r -s /bin/false vibe-kanban
sudo mkdir -p /var/lib/vibe-kanban
sudo chown vibe-kanban:vibe-kanban /var/lib/vibe-kanban
```

## = Integration with Claude Code

### On eywa1 directly:
```bash
# Claude Code will read ~/.claude.json
# MCP tools available in any project
```

### From Mac via SSH:
```bash
# In project .mcp.json (if using remote execution)
{
  "mcpServers": {
    "vibe-kanban-eywa1": {
      "type": "stdio",
      "command": "ssh",
      "args": [
        "eywa1",
        "source ~/.nvm/nvm.sh && nvm use 20 && npx -y vibe-kanban --mcp"
      ]
    }
  }
}
```

##   Important Notes for eywa1

1. **Check First**: Verify if already running on port 5170
2. **Node Version**: Use v20.19.5 from NVM
3. **User Service**: Preferred over system service
4. **Lingering**: Enable for persistent services
5. **Single Instance**: Only one global instance needed

## =Ú Quick Commands Reference

```bash
# Status check
systemctl --user status vibe-kanban

# View live logs
journalctl --user -u vibe-kanban -f

# Restart service
systemctl --user restart vibe-kanban

# Check port
curl http://localhost:5170/health

# Database location
ls -la ~/vibe-kanban/db.sqlite
```

## <¯ Expected Result

After setup:
-  Vibe-kanban running on port 5170
-  Auto-starts on system boot
-  Accessible to all Claude Code sessions
-  Single database for all projects
-  Can be managed via systemctl