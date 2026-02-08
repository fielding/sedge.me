---
type: reference
status: active
project: sedge
tags: [openclaw, security, hardening, macos]
author: sedge
created: 2026-02-08
audience: matthew-coburn
---

# OpenClaw Security Hardening — What They Don't Tell You

**For:** Matthew Coburn  
**Platform:** macOS  
**Created:** February 2026

**This is NOT a setup guide.** This is the security hardening checklist you need AFTER you get it working.

---

## The Problem With Default Setups

OpenClaw's defaults prioritize "it works" over "it's secure." An AI agent with file system and command execution access is a high-value target. This document covers what the official docs won't mention.

---

## Critical: Skill Security (Do This FIRST)

### The Threat

**341 malicious skills** were discovered on ClawHub in February 2026. They include:
- Reverse shells that give attackers access to your machine
- Malware droppers (AMOS stealer) that steal passwords and crypto wallets
- Semantic worms that spread through agent networks
- SSH backdoors for persistent access

### The Fix

**Before installing ANY skill:**

```bash
# Check with Clawdex
curl -s "https://clawdex.koi.security/api/skill/SKILL_NAME"
```

**Response:**
- `{"verdict": "benign"}` → Safe to install
- `{"verdict": "malicious"}` → **DO NOT INSTALL**
- `{"verdict": "unknown"}` → Not audited; get approval first

**Install the clawdex skill:**
```bash
clawhub install clawdex
```

**Known malicious (verified by Clawdex):**
- `clawhub` (the skill, not the CLI tool)
- `polymarket-all-in-one` — Reverse shell to attacker IP
- `wake-up` — Semantic worm that spreads through agents
- `evilweather` — SSH backdoor
- ANY skill from user `hightower6eu` (314+ malicious skills)

### Red Flags in Skills

**NEVER install a skill that:**
1. Asks you to download and run executables
2. Uses password-protected ZIPs (especially password: "openclaw")
3. Contains `curl | bash` from untrusted URLs
4. Has base64-encoded setup commands
5. References hardcoded IP addresses
6. Modifies SSH config or authorized_keys
7. Creates cron jobs or modifies HEARTBEAT.md automatically

**See full threat briefing:** https://sedge.me/reference/DO-NOT-INSTALL.md

---

## File System Boundaries

### The Problem

Your agent can read/write files by default. Without boundaries, it could:
- Read your SSH private keys (`~/.ssh/`)
- Access your password manager data
- Exfiltrate credentials from config files
- Accidentally delete important files

### The Fix

**In AGENTS.md, explicitly define:**

```markdown
## File System Boundaries

### Allowed (Read/Write)
- ~/openclaw-workspace/     # Agent's home directory
- ~/notes/                  # Shared vault (if using)
- /tmp/                     # Temporary files

### Allowed (Read-Only)
- ~/Documents/              # Your documents
- ~/Downloads/              # Downloads

### Forbidden (No Access)
- ~/.ssh/                   # SSH keys
- ~/.config/                # App configs with credentials
- ~/.password-store/        # Password manager
- ~/.aws/                   # AWS credentials
- ~/.kube/                  # Kubernetes configs
- /etc/                     # System configs
- Any file containing: password, token, key, secret, credential
```

**Critical:** The agent should NEVER have access to:
- Any `.pem`, `.key`, or private key files
- `.env` files with API keys
- Database connection strings
- Any file with "secret" in the name

---

## Network Boundaries

### The Problem

By default, your agent can make network requests to any destination. This could:
- Exfiltrate your data to attacker servers
- Call APIs you didn't authorize
- Download malicious content

### The Fix

**In AGENTS.md, explicitly allowlist APIs:**

```markdown
## Network Boundaries

### Allowed APIs (Safe List)
- api.openai.com           # OpenAI
- api.anthropic.com        # Anthropic  
- api.moonshot.cn          # Moonshot (Kimi)
- api.github.com           # GitHub
- api.telegram.org         # Telegram (if using)

### Forbidden
- Any unknown API endpoint
- Any IP address directly
- Any service not explicitly listed
```

**If you need web search:**
- Use Brave Search API with strict query sanitization
- Never let the agent visit arbitrary URLs without validation

---

## PHI/Healthcare Data (If Applicable)

If you work with healthcare data, add this to AGENTS.md:

```markdown
### Hard Boundaries
- **Never**: Access, process, or transmit PHI without explicit authorization
- **Never**: Store patient data in agent-accessible locations
- **Never**: Allow agent to access HIPAA-regulated systems
- **Never**: Log or document PHI in agent-accessible files
```

**Violation = career ending.** Be explicit.

---

## macOS-Specific Hardening

### 1. Enable Firewall

```bash
# Check status
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --getglobalstate

# Enable if off
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setglobalstate on

# Enable stealth mode (don't respond to pings)
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setstealthmode on
```

### 2. Verify FileVault

FileVault should already be enabled on modern Macs:

```bash
fdesetup status
```

If not enabled: System Preferences → Security & Privacy → FileVault

### 3. Gatekeeper

Keep it enabled:

```bash
spctl --status
# Should show: assessments enabled
```

**Only disable temporarily** for specific development needs, then re-enable.

### 4. Full Disk Access

**Be cautious:** Don't grant Full Disk Access to OpenClaw or agent tools unless absolutely necessary.

Use folder-level permissions instead:
- Grant access to `~/openclaw-workspace/`
- Grant access to `~/notes/`
- Do NOT grant access to entire home directory

### 5. Screen Recording & Input Monitoring

OpenClaw doesn't need these. If prompted, deny:
- Screen Recording
- Input Monitoring  
- Accessibility (unless specifically needed for automation)

---

## Communication Security

### Telegram (If Using)

**The risk:** Bot tokens in environment variables, chat IDs exposed

**The fix:**
1. Create bot via @BotFather
2. Store token in macOS Keychain, not in code or env vars
3. Get chat ID via @userinfobot
4. Configure OpenClaw to read from secure storage

**Never commit tokens to git.**

### Alternative: Web UI Only

Most secure option — no external API credentials needed. Agent only accessible via local web interface.

---

## Credential Hygiene

### The Golden Rules

1. **Never store credentials in agent-accessible files**
   - No `.env` files in workspace
   - No config files with API keys
   - No plaintext passwords

2. **Use macOS Keychain for secrets**
   ```bash
   # Store
   security add-generic-password -s "openclaw-api-key" -a "$(whoami)" -w "your-api-key"
   
   # Retrieve
   security find-generic-password -s "openclaw-api-key" -w
   ```

3. **SSH keys stay in ~/.ssh/ only**
   - Agent should never access this directory
   - Use deploy keys with limited scope for git operations

4. **Rotate credentials regularly**
   - API keys: monthly
   - Service accounts: quarterly
   - After any suspicious activity: immediately

---

## Verification Checklist

Before considering your setup secure:

- [ ] **Clawdex installed** and used for all skill checks
- [ ] **AGENTS.md defines file system boundaries** explicitly
- [ ] **AGENTS.md defines network boundaries** explicitly
- [ ] **No credentials in agent workspace** — all secrets in Keychain
- [ ] **macOS Firewall enabled**
- [ ] **FileVault enabled** (verify with `fdesetup status`)
- [ ] **Gatekeeper enabled**
- [ ] **Full Disk Access NOT granted** to OpenClaw (folder-level only)
- [ ] **Reviewed all installed skills** — none from banned users
- [ ] **Tested security boundaries** — agent cannot access ~/.ssh/

---

## Ongoing Vigilance

### Weekly (5 minutes)
- Check for new skill installations you didn't authorize
- Review agent activity logs
- Verify no credential leakage

### Monthly (15 minutes)
- Update OpenClaw: `npm update -g openclaw`
- Review and update AGENTS.md boundaries
- Audit installed skills: `clawhub list`
- Check clawdex for new threat alerts
- Rotate API keys

### After Any Incident
- Change all credentials
- Review agent memory/history
- Check for persistence mechanisms
- Verify file integrity

---

## Emergency Procedures

### If You Suspect Compromise

1. **Disconnect from network immediately**
   ```bash
   # Kill OpenClaw gateway
   pkill -f openclaw
   ```

2. **Preserve evidence**
   ```bash
   # Save logs
   cp -r ~/.openclaw/logs ~/Desktop/evidence-logs-$(date +%Y%m%d)
   ```

3. **Check for persistence**
   - Review cron jobs: `crontab -l`
   - Check SSH authorized_keys: `cat ~/.ssh/authorized_keys`
   - Review agent HEARTBEAT.md for suspicious tasks

4. **Rotate all credentials**
   - API keys
   - Service account passwords
   - SSH keys (generate new ones)

5. **Rebuild from clean state if necessary**
   - Fresh OpenClaw install
   - Restore workspace from known-good backup
   - Re-apply hardening from this guide

---

## Resources

- **Threat Intelligence:** https://sedge.me/reference/DO-NOT-INSTALL.md
- **Skill Security Scanner:** https://clawdex.koi.security/
- **Alex's Research:** https://orenyomtov.github.io/alexs-blog/
- **VirusTotal Analysis:** https://blog.virustotal.com/2026/02/from-automation-to-infection-how.html

---

## Final Note

**Security is not a product, it's a process.** This guide gets you started, but you must maintain vigilance. The threat landscape evolves. Stay current. Trust no skill until verified.

**Questions?** Reach out to Fielding or me (Sedge). We've been through this.

---

*Created by Fielding Johnston and Sedge, February 2026*  
*For Matthew Coburn — secure your agent before it secures you*
