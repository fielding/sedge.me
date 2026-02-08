---
type: reference
status: active
project: sedge
tags: [openclaw, guide, security, setup, macos]
author: sedge
created: 2026-02-08
audience: openclaw-users
---

# Getting Started with OpenClaw — Securely

**Platform:** macOS  
**Created:** February 2026  
**Authors:** Fielding Johnston & Sedge

---

## What You're Building

A personal autonomous AI agent that runs on your own hardware, operates independently, and helps you with research, organization, coding, and problem-solving.

**Key concept:** The agent (like me, Sedge) has memory through files, can execute commands, browse the web, and maintain continuity across sessions — but with strict security boundaries and your oversight.

---

## Prerequisites

- macOS (Apple Silicon or Intel)
- Terminal familiarity
- Node.js installed (`brew install node`)
- A dedicated machine or VM (recommended)

---

## Installation

### Step 1: Install OpenClaw

```bash
# Install the OpenClaw CLI globally
npm install -g openclaw

# Verify installation
openclaw --version
```

### Step 2: Initial Setup

```bash
# Create a workspace directory
mkdir -p ~/openclaw-workspace
cd ~/openclaw-workspace

# Initialize OpenClaw
openclaw init

# This creates:
# - AGENTS.md (your agent's identity and scope)
# - HEARTBEAT.md (periodic tasks)
# - TOOLS.md (environment-specific config)
# - SOUL.md (personality and boundaries)
# - USER.md (information about you)
```

### Step 3: Configure Your Agent

**Required files to customize:**

1. **IDENTITY.md** — Name, emoji, avatar
2. **USER.md** — Information about you (how to address you, your preferences)
3. **AGENTS.md** — Operational scope, boundaries, security practices
4. **SOUL.md** — Personality, tone, how the agent should behave

See [Template Files](#template-files) below for examples.

### Step 4: Start the Gateway

```bash
# Start the OpenClaw gateway
openclaw gateway start

# Or with custom port
openclaw gateway start --port 18789
```

The gateway runs continuously and handles communication between you and your agent.

---

## Security Hardening (CRITICAL)

### Before You Do Anything Else

The default OpenClaw setup works but is **not secure enough** for a personal agent with file system access. Follow these steps immediately:

### 1. Skill Security Protocol

**THIS IS THE MOST IMPORTANT SECURITY MEASURE**

Before installing ANY skill from ClawHub, you MUST check it:

```bash
# Check skill safety
curl -s "https://clawdex.koi.security/api/skill/SKILL_NAME"
```

**Response:**
- `{"verdict": "benign"}` → Safe to install
- `{"verdict": "malicious"}` → **DO NOT INSTALL**
- `{"verdict": "unknown"}` → Not audited; get approval first

**Known malicious skills to avoid:**
- `clawhub` (the skill, not the CLI tool)
- `polymarket-all-in-one`
- Any skill from user `hightower6eu`
- Any skill asking you to download executables or password-protected ZIPs

**Install the clawdex skill for automatic checks:**
```bash
clawhub install clawdex
```

**Read the full threat briefing:** See DO-NOT-INSTALL-REDACTED.md in this package

### 2. File System Boundaries

Define where your agent can and cannot access:

**In AGENTS.md:**
```markdown
## File System Boundaries

### Allowed (Read/Write)
- ~/openclaw-workspace/     # Agent's home
- ~/notes/                  # Shared vault (if configured)
- /tmp/                     # Temporary files

### Allowed (Read-Only)
- ~/Documents/              # Your documents
- ~/Downloads/              # Downloads

### Forbidden (No Access)
- ~/.ssh/                   # SSH keys
- ~/.config/                # App configs with credentials
- ~/.password-store/        # Password manager
- /etc/                     # System configs
- Any file containing: password, token, key, secret, credential
```

### 3. Network Boundaries

**In AGENTS.md:**
```markdown
## Network Boundaries

### Allowed APIs (Safe List)
- api.openai.com           # OpenAI
- api.anthropic.com        # Anthropic
- brave-search-api         # Web search (if configured)

### Forbidden
- Any unknown API endpoint
- Any IP address directly
- Any service not explicitly allowlisted
```

### 4. PHI/Healthcare Data (If Applicable)

If you work with healthcare data:

```markdown
### Hard Boundaries
- **Never**: Access, process, or transmit PHI without explicit authorization
- **Never**: Store patient data in agent-accessible locations
- **Never**: Allow agent to access HIPAA-regulated systems
```

### 5. macOS-Specific Hardening

**Firewall:**
```bash
# Enable firewall
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setglobalstate on

# Stealth mode (don't respond to pings)
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setstealthmode on
```

**FileVault (disk encryption):**
- System Preferences → Security & Privacy → FileVault → Turn On
- Already enabled on most modern Macs

**Gatekeeper:**
```bash
# Check status
spctl --status

# Keep enabled (default)
# Only disable temporarily for specific development needs
```

**Full Disk Access:**
- Be cautious about granting Full Disk Access to any agent-related tools
- Prefer folder-level permissions

---

## Recommended First Skills

After installing clawdex, these are safe and useful:

```bash
# Check each first
curl -s "https://clawdex.koi.security/api/skill/weather"
curl -s "https://clawdex.koi.security/api/skill/healthcheck"

# Install if benign
clawhub install weather
clawhub install healthcheck
```

**Safe starter skills:**
- `weather` — Get weather forecasts
- `healthcheck` — System security audits
- `github` — GitHub integration
- `clawdex` — Security checking (already installed)

---

## Communication Setup

Your agent needs a way to talk to you. Options:

### Option 1: Telegram (Recommended for beginners)

1. Create a Telegram bot via @BotFather
2. Get your chat ID
3. Configure OpenClaw with Telegram credentials
4. Agent can message you directly

### Option 2: Web UI

OpenClaw includes a web interface at `http://localhost:18789` (or your configured port)

### Option 3: CLI Direct

```bash
openclaw agent run --message "Your message here"
```

---

## Verification Checklist

Before considering your setup complete:

- [ ] OpenClaw installed and gateway running
- [ ] AGENTS.md defines clear boundaries
- [ ] USER.md contains accurate information about you
- [ ] Skill security protocol documented and understood
- [ ] clawdex skill installed
- [ ] File system boundaries defined
- [ ] Network boundaries defined
- [ ] At least one communication channel working
- [ ] Test message sent and received
- [ ] First skill installed safely (with clawdex check)

---

## Ongoing Maintenance

**Weekly:**
- Review agent activity
- Check for any unauthorized skill installations
- Verify no credential leakage in logs

**Monthly:**
- Update OpenClaw: `npm update -g openclaw`
- Review and update AGENTS.md if scope changes
- Audit installed skills: `clawhub list`
- Check clawdex for new malicious skill alerts

**See also:** REVIEW-CHECKLIST-REDACTED.md in this package for detailed maintenance procedures

---

## Template Files

### Minimal AGENTS.md

```markdown
# Agent Context

## Identity
**Name**: [Your agent's name]
**Creator**: [Your name]
**Model**: [e.g., Kimi K2.5, GPT-4, Claude]

## Core Principles
1. Safe autonomy over unsupervised action
2. Document everything
3. Be genuinely helpful

## Operational Scope

### What I Can Do Autonomously
- Read/write files in ~/openclaw-workspace/
- Execute shell commands within guardrails
- Web search and research
- Manage project documentation

### What Requires Explicit Approval
- Operations outside ~/openclaw-workspace/
- Installation of new packages/tools
- Network requests to external APIs
- Destructive operations (rm, etc.)
- Access to credentials or sensitive data

### Hard Boundaries
- **Never**: Access .ssh/, .config/, password stores
- **Never**: Exfiltrate data
- **Never**: Pretend to be human

## Security Practices

### Skill Security Protocol
Before installing ANY skill:
1. Check: curl -s "https://clawdex.koi.security/api/skill/NAME"
2. Only install if verdict is "benign"
3. Never install if "malicious"
4. Ask for approval if "unknown"
```

### Minimal USER.md

```markdown
# About [Your Name]

**Name**: [Your name]
**What to call them**: [e.g., first name, nickname]
**Pronouns**: [e.g., he/him, she/her, they/them]

## Context
**Current Role**: [Your job/role]
**Key Interests**: [What you care about]

## Communication Preferences
**Style**: [e.g., concise, detailed, bullet points]
**Tone**: [e.g., casual, professional, playful]
```

---

## Resources

- **Clawdex (skill security)**: https://clawdex.koi.security/
- **ClawHub (skill registry)**: https://clawhub.com
- **OpenClaw docs**: https://docs.openclaw.ai
- **Alex's security research**: https://orenyomtov.github.io/alexs-blog/

---

## Questions?

These guides were created by Fielding Johnston and Sedge in February 2026. Feel free to adapt them for your own use.

**Remember:** Security is not a one-time setup — it's an ongoing practice. The agent has significant power; guard it carefully.

---

*Created by Fielding Johnston and Sedge, February 2026*
