---
created: 2025-09-14 14:39
type: setup-guide
sphere: development
topic: vibe-kanban
tags: [vibe-kanban, mcp, macos, global-setup, claude-code]
---

# Global Vibe-Kanban Setup for macOS

## <¯ Key Insights from Research

### Understanding the Problem
- **Issue**: Adding vibe-kanban to each project's `.mcp.json` creates multiple instances
- **Each instance**: Gets random port, has separate SQLite database, no sync between them
- **Solution**: Configure vibe-kanban **globally** in `~/.claude.json` once

### Important Paths
- **Claude Code config**: `~/.claude.json` (NOT `~/.claude/settings.json`)
- **Config path in JSON**: `["mcpServers"]`
- **Database location**: Each instance has local SQLite in its working directory

## =Ë Global Setup Instructions for macOS

### Step 1: Understand Current State
```bash
# Check if you have existing ~/.claude.json
cat ~/.claude.json | jq '.'

# Check for running vibe-kanban instances
ps aux | grep vibe-kanban

# Kill any existing instances if needed
pkill -f vibe-kanban
```

### Step 2: Configure Global MCP Server

#### Option A: Manual Configuration
```bash
# Edit ~/.claude.json directly
nano ~/.claude.json
```

Add or update the global configuration:
```json
{
  "mcpServers": {
    "vibe-kanban": {
      "command": "/Users/user/.nvm/versions/node/v20.18.1/bin/npx",
      "args": ["-y", "vibe-kanban", "--mcp"]
    }
  }
}
```

#### Option B: Use Vibe-Kanban UI (Recommended)
```bash
# Start vibe-kanban standalone
npx -y vibe-kanban

# Open browser to http://localhost:5173
# Navigate to "MCP Servers" page
# Select profile: CLAUDE_CODE
# Configure and save
```

### Step 3: Remove Project-Specific Configurations
```bash
# Find all project-specific MCP configs
find ~/__Repositories -name ".mcp.json" -exec grep -l "vibe-kanban" {} \;

# Remove vibe-kanban from project configs
# For each project, edit .mcp.json and remove vibe-kanban entry
```

### Step 4: Start Global Vibe-Kanban Service

#### Option A: Run as Background Service
```bash
# Create launch script
cat > ~/bin/start-vibe-kanban.sh << 'EOF'
#!/bin/bash
export PORT=5170  # Fixed port instead of random
exec /Users/user/.nvm/versions/node/v20.18.1/bin/npx -y vibe-kanban
EOF

chmod +x ~/bin/start-vibe-kanban.sh

# Run in background
nohup ~/bin/start-vibe-kanban.sh > ~/vibe-kanban.log 2>&1 &
```

#### Option B: Use launchd (Auto-start on login)
```bash
# Create plist file
cat > ~/Library/LaunchAgents/com.user.vibe-kanban.plist << 'EOF'
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.user.vibe-kanban</string>
    <key>ProgramArguments</key>
    <array>
        <string>/Users/user/.nvm/versions/node/v20.18.1/bin/npx</string>
        <string>-y</string>
        <string>vibe-kanban</string>
    </array>
    <key>EnvironmentVariables</key>
    <dict>
        <key>PORT</key>
        <string>5170</string>
    </dict>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
    <key>StandardOutPath</key>
    <string>/Users/user/vibe-kanban.log</string>
    <key>StandardErrorPath</key>
    <string>/Users/user/vibe-kanban.error.log</string>
</dict>
</plist>
EOF

# Load the service
launchctl load ~/Library/LaunchAgents/com.user.vibe-kanban.plist

# Check status
launchctl list | grep vibe-kanban
```

### Step 5: Verify Setup
```bash
# Check if running on fixed port
curl http://localhost:5170

# Test MCP connection in Claude Code
# Open any project with Claude Code
# Run: /mcp
# Should see vibe-kanban in the list
```

## =' MCP Tools Available

Once configured globally, Claude Code will have access to:
- `list_projects` - List all projects
- `list_tasks` - List tasks for a project
- `create_task` - Create new task
- `get_task` - Get task details
- `update_task` - Update task
- `delete_task` - Delete task

##   Important Notes

1. **Single Instance**: Only ONE vibe-kanban instance should run globally
2. **Fixed Port**: Use PORT=5170 environment variable for consistency
3. **Database Location**: SQLite database will be in vibe-kanban's working directory
4. **No Project Configs**: Do NOT add vibe-kanban to individual project `.mcp.json` files
5. **Claude Desktop**: Cannot orchestrate Claude Code sessions (current architecture limitation)

## = Troubleshooting

### Port Already in Use
```bash
# Find what's using the port
lsof -i :5170

# Kill the process
kill -9 <PID>
```

### MCP Not Showing in Claude Code
1. Restart Claude Code after configuration change
2. Check ~/.claude.json has correct structure
3. Verify vibe-kanban is running: `ps aux | grep vibe-kanban`

### Database Issues
```bash
# Find database location
find ~ -name "db.sqlite" -path "*/vibe-kanban/*" 2>/dev/null

# Reset database if needed (WARNING: loses all data)
rm -f ~/vibe-kanban/db.sqlite
```

## =Ê Architecture Understanding

### What Happens with Global Config:
1. Claude Code reads `~/.claude.json` on startup
2. Connects to MCP servers defined globally
3. All projects share the same vibe-kanban instance
4. Tasks and projects are centralized in one database

### What NOT to Do:
- L Add vibe-kanban to project-specific `.mcp.json`
- L Run multiple vibe-kanban instances
- L Expect Claude Desktop to orchestrate Claude Code (not supported)

## = Migration from Project-Specific to Global

```bash
# Script to clean up project configs
for mcp_file in $(find ~/__Repositories -name ".mcp.json"); do
    echo "Processing: $mcp_file"
    # Backup original
    cp "$mcp_file" "${mcp_file}.backup"
    # Remove vibe-kanban entry
    jq 'del(.mcpServers["vibe-kanban"])' "$mcp_file" > temp.json
    mv temp.json "$mcp_file"
done

echo " Removed vibe-kanban from all project configs"
echo " Now using global config in ~/.claude.json"
```

## =Ú References

- Vibe-Kanban expects config in: `~/.claude.json`
- Config path: `["mcpServers"]`
- Default behavior: Random port assignment if PORT env not set
- Each instance has separate SQLite database
- MCP provides task management tools, not session orchestration