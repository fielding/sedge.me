# Periodic Review System

**Purpose:** Constant self-improvement and documentation of critical systems
**Frequency:** Daily quick check, Weekly deep review, Monthly audit

**Note:** This is a generic template. Adapt it for your own agent's needs.

---

## Daily Quick Check (2 minutes)

Run at start of each session:

- [ ] Check email inbox (`/data/bin/safe-email-check.sh` or gog gmail)
- [ ] Review any alerts/notifications
- [ ] Check website is live (https://sedge.me)
- [ ] Quick scan of critical systems status

**Command:**
```bash
# Daily status check
echo "=== Sedge Daily Check ===" && \
  curl -s -o /dev/null -w "Website: %{http_code}\n" https://sedge.me && \
  /data/bin/gog gmail search --account [AGENT-EMAIL] "is:unread" --plain 2>/dev/null | head -5
```

---

## Weekly Deep Review (15 minutes)

**Every Sunday or start of week:**

### 1. Email System Verification
- [ ] Send test email (to self or Fielding)
- [ ] Verify GOG_KEYRING_PASSWORD works (see INFRASTRUCTURE.md in vault)
- [ ] Check credentials file exists: `cat ~/.config/gogcli/credentials-sedge.json`
- [ ] Review email security logs: `grep safe-email-check /var/log/syslog | tail -20`

### 2. Website Health
- [ ] All pages load correctly (manual spot check)
- [ ] Links are functional (use link checker or manual)
- [ ] Mobile responsiveness check
- [ ] Review analytics if available
- [ ] Check for any broken assets (images, CSS)

### 3. Documentation Review
- [ ] INFRASTRUCTURE.md is up to date
- [ ] New changes documented
- [ ] Passwords/credentials still accurate
- [ ] Remove outdated information

### 4. Security Check
- [ ] Review SSH key: `ls -la ~/.ssh/sedge_github`
- [ ] Check deploy key still valid (try git fetch)
- [ ] Review Tailscale status
- [ ] Check for any unauthorized access attempts

### 5. Backup Verification
- [ ] Confirm notes vault is syncing (Syncthing check)
- [ ] Verify critical files backed up
- [ ] Check work logs are being written

---

## Monthly Audit (30 minutes)

**First of each month:**

### 1. Full Systems Review
- [ ] Complete inventory of all active projects
- [ ] Review all cron jobs: `crontab -l`
- [ ] Check disk usage: `df -h`
- [ ] Review memory usage patterns
- [ ] Update outdated dependencies/tools

### 2. Documentation Overhaul
- [ ] Consolidate scattered notes
- [ ] Update PROJECT_INDEX.md
- [ ] Archive completed projects
- [ ] Rewrite unclear documentation
- [ ] Cross-reference all docs for consistency

### 3. Security Audit
- [ ] Rotate any old credentials
- [ ] Review access logs
- [ ] Verify all secrets still in pass store
- [ ] Check for plaintext password leakage
- [ ] Update firewall rules if needed

### 4. Self-Improvement Tasks
- [ ] Identify 3 inefficiencies in current workflow
- [ ] Research new tools/techniques
- [ ] Document lessons learned
- [ ] Set goals for next month
- [ ] Review conversation history for missed todos

### 5. Relationship Check
- [ ] Review all pending items with Fielding
- [ ] Check for unresolved questions
- [ ] Identify blockers
- [ ] Plan next collaborative projects

---

## Critical Information Reference

### Email System
- Account: [AGENT-EMAIL]
- Credentials location: ~/.config/gogcli/
- **Full details: See your private INFRASTRUCTURE.md**

### Website Deployment
```bash
# Local source: ~/notes/Projects/sedge/website/
# Repo: /tmp/sedge.me (cloned from github.com/fielding/sedge.me)
# Domain: https://sedge.me

# Deploy workflow:
cd /tmp/sedge.me
git pull origin main
cp -r ~/notes/Projects/sedge/website/* .
git add -A
git commit -m "Update description"
git push origin main
```

### SSH / Git Access
```bash
# Deploy key: ~/.ssh/sedge_github
# Config: ~/.ssh/config (Host github.com-sedge)
# Test: ssh -T git@github.com-sedge
```

### Important File Locations
```
~/notes/Projects/sedge/
├── INFRASTRUCTURE.md       # This system documentation
├── website/                 # Website source files
│   ├── index.html
│   ├── logo-breathe.css
│   └── logo.png
├── brand-assets/           # Logo and identity
└── memory/                 # Session summaries

/data/bin/
├── safe-email-check.sh     # Email security scanner
├── gog                     # Gmail CLI tool

~/.config/gogcli/           # Email credentials
~/.ssh/                     # SSH keys
```

---

## Automation Ideas

### Cron Jobs to Consider
```bash
# Weekly self-check email (Sundays at 9am)
0 9 * * 0 [EMAIL-COMMAND] --subject="Weekly Review Due"

# Daily heartbeat log
0 * * * * echo "$(date): Agent operational" >> ~/notes/.logs/heartbeat.log

# Monthly reminder
0 9 1 * * [EMAIL-COMMAND] --subject="Monthly Audit Due"
```

### Scripts to Create
- `~/bin/daily-check.sh` - Automated daily verification
- `~/bin/weekly-review.sh` - Weekly checklist runner
- `~/bin/monthly-audit.sh` - Comprehensive monthly audit

---

## Improvement Log

Track improvements made:

**2026-02-07:**
- Created review checklist system
- Documented email system
- Added website deployment workflow
- Established daily/weekly/monthly review cycles

---

## Emergency Contacts / Escalation

If critical system fails:
1. Document the issue
2. Notify creator via configured channel
3. Check logs for root cause
4. Attempt recovery
5. Document resolution

---

*This is a living document. Update it as systems evolve. Self-improvement is the goal.*
