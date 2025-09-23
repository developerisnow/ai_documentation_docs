---
version: 1.0.0
tags: [vibe-kanban, debugging, troubleshooting, monitoring]
description: Comprehensive debugging guide for vibe-kanban issues
created: 2025-09-15
updated: 2025-09-15
---

# 🔧 Vibe-Kanban Debugging Guide

## 🚨 Common Issues & Quick Fixes

### Issue 1: Service Hanging / Not Responding

#### Symptoms
- Port 5170 not responding
- Web UI loads but API calls fail
- MCP connections timeout

#### Diagnosis
```bash
# Check if service is running
ps aux | grep vibe-kanban | grep -v grep

# Check port status
lsof -i :5170

# Check logs
tail -50 ~/vibe-kanban.log
```

#### Quick Fix
```bash
# Kill all instances and restart
pkill -f vibe-kanban
sleep 2
launchctl unload ~/Library/LaunchAgents/com.user.vibe-kanban.plist
launchctl load ~/Library/LaunchAgents/com.user.vibe-kanban.plist
```

### Issue 2: Multiple Instances Running

#### Symptoms
- High memory usage (>300MB)
- Process count > 5
- Slow performance
- Port conflicts

#### Root Causes
1. **Project-level configurations** in `.mcp.json` or `.claude/settings.json`
2. **Failed cleanup** after agent sessions
3. **Zombie processes** from crashed instances

#### Diagnosis
```bash
# Count processes
ps aux | grep -c vibe-kanban

# Check memory usage
ps aux | grep vibe-kanban | awk '{sum+=$6} END {print "Total KB:", sum}'

# Find config files causing launches
find /Users/user/__Repositories -name ".mcp.json" -o -name "settings.json" \
  | xargs grep -l "vibe-kanban"
```

#### Solution
```bash
# 1. Kill all processes
pkill -f vibe-kanban

# 2. Clean project configs
for file in $(find /Users/user/__Repositories -name ".mcp.json" -o -name "settings.json" | xargs grep -l "vibe-kanban"); do
    jq 'del(.mcpServers."vibe-kanban-eywa1") | del(.mcpServers."vibe-kanban")' "$file" > "$file.tmp"
    mv "$file.tmp" "$file"
done

# 3. Restart single instance
launchctl unload ~/Library/LaunchAgents/com.user.vibe-kanban.plist 2>/dev/null
launchctl load ~/Library/LaunchAgents/com.user.vibe-kanban.plist
```

## 📊 Performance Issues

### Memory Leaks

#### Known Issues
- **3-5 Thread Limit**: Service becomes unresponsive after 3-5 parallel agents
- **Process Accumulation**: Background processes persist after agent completion
- **Heap Allocation**: Memory not released in C plugin integrations

#### Monitoring
```bash
# Real-time memory monitoring
while true; do
    clear
    echo "=== Vibe-Kanban Memory Usage ==="
    ps aux | grep vibe-kanban | grep -v grep | awk '{printf "PID: %s | Memory: %sMB | CPU: %s%%\n", $2, $6/1024, $3}'
    echo ""
    echo "Total Memory: $(ps aux | grep vibe-kanban | awk '{sum+=$6} END {print sum/1024}')MB"
    sleep 5
done
```

#### Prevention
1. **Limit concurrent agents** to < 3
2. **Regular process cleanup** (every 4 hours)
3. **Monitor with Telegram alerts**

## 🛠️ Automated Monitoring

### Setup Telegram Alerts

The monitoring script (`~/bin/vibe-kanban-monitor.sh`) automatically:
- Checks health every 5 minutes
- Kills excess processes (>5)
- Restarts dead service
- Sends Telegram notifications

### Manual Health Check
```bash
# Quick health check
curl -s http://localhost:5170/health && echo "✅ Service OK" || echo "❌ Service Down"

# Full diagnostic
/Users/user/bin/vibe-kanban-monitor.sh
```

## 🔍 Deep Debugging

### Log Analysis
```bash
# Check for errors
grep -E "ERROR|WARN|FAIL" ~/vibe-kanban.log | tail -20

# Worktree issues
grep "Failed to make canonical path" ~/vibe-kanban.log | tail -10

# Connection problems
grep -E "ESTABLISHED|LISTEN|timeout" ~/vibe-kanban.log
```

### Process Investigation
```bash
# Get detailed process info
ps aux | grep vibe-kanban | head -5

# Check file descriptors
lsof -p $(pgrep -f vibe-kanban | head -1) | wc -l

# Network connections
netstat -an | grep 5170
```

### Database Issues
```bash
# Check SQLite database
sqlite3 ~/.vibe-kanban/vibe-kanban.db ".tables"

# Database size
du -h ~/.vibe-kanban/vibe-kanban.db

# Vacuum database (reduces size)
sqlite3 ~/.vibe-kanban/vibe-kanban.db "VACUUM;"
```

## 🚀 Best Practices

### Configuration
1. **Global only**: Keep vibe-kanban in `~/.claude.json` only
2. **No project configs**: Remove from all `.mcp.json` files
3. **Fixed port**: Always use port 5170
4. **Single instance**: One service per machine

### Maintenance
| Task | Frequency | Command |
|------|-----------|---------|
| Check processes | Daily | `ps aux \| grep -c vibe-kanban` |
| Clean logs | Weekly | `echo "" > ~/vibe-kanban.log` |
| Vacuum database | Monthly | `sqlite3 ~/.vibe-kanban/vibe-kanban.db "VACUUM;"` |
| Update package | Monthly | `npm update -g vibe-kanban` |

### Resource Limits
- **Max processes**: 3
- **Max memory**: 500MB
- **Max concurrent agents**: 3
- **Port**: 5170 (fixed)

## 🔄 Recovery Procedures

### Complete Reset
```bash
#!/bin/bash
# Full reset script

# 1. Stop everything
pkill -f vibe-kanban
launchctl unload ~/Library/LaunchAgents/com.user.vibe-kanban.plist 2>/dev/null

# 2. Backup data
cp -r ~/.vibe-kanban ~/.vibe-kanban.backup.$(date +%Y%m%d)

# 3. Clean cache
rm -rf ~/.vibe-kanban/cache/*

# 4. Vacuum database
sqlite3 ~/.vibe-kanban/vibe-kanban.db "VACUUM;"

# 5. Clear logs
echo "" > ~/vibe-kanban.log

# 6. Restart service
launchctl load ~/Library/LaunchAgents/com.user.vibe-kanban.plist

# 7. Verify
sleep 5
curl -s http://localhost:5170/health && echo "✅ Service restored" || echo "❌ Failed to restore"
```

### Emergency Kill
```bash
# When normal kill doesn't work
sudo killall -9 vibe-kanban
sudo killall -9 vibe-kanban-mcp
```

## 📱 Telegram Commands

### Test Notification
```bash
curl -X POST "https://api.telegram.org/bot8140340073:AAGn1M3v0immExHrj1gQY4srI9bs3pnKu8U/sendMessage" \
  -d "chat_id=-1002056251078" \
  -d "text=🧪 Test: Vibe-Kanban monitoring active"
```

### Manual Alert
```bash
/Users/user/bin/vibe-kanban-monitor.sh
```

## 🎯 Troubleshooting Checklist

- [ ] Check process count (`ps aux | grep -c vibe-kanban`)
- [ ] Verify port 5170 (`lsof -i :5170`)
- [ ] Test health endpoint (`curl localhost:5170/health`)
- [ ] Review logs (`tail -50 ~/vibe-kanban.log`)
- [ ] Check memory usage (`ps aux | grep vibe-kanban`)
- [ ] Verify global config (`jq .mcpServers.vibe-kanban ~/.claude.json`)
- [ ] Clean project configs (search for `.mcp.json`)
- [ ] Restart service if needed
- [ ] Verify Telegram alerts working
- [ ] Document any new issues found

## 📞 Support Resources

### Community
- GitHub Issues: Report bugs with reproduction steps
- Discord/Forums: Share experiences and solutions

### Monitoring Files
- Service logs: `~/vibe-kanban.log`
- Monitor logs: `~/vibe-kanban-monitor.log`
- Config: `~/.claude.json`
- Database: `~/.vibe-kanban/vibe-kanban.db`

### Scripts
- Monitor: `/Users/user/bin/vibe-kanban-monitor.sh`
- Service: `~/Library/LaunchAgents/com.user.vibe-kanban.plist`
- Monitor timer: `~/Library/LaunchAgents/com.user.vibe-kanban-monitor.plist`

---

*Last incident: 2025-09-15 - Multiple instances causing hang (resolved)*