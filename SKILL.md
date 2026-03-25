---
name: hardening
description: "Complete security hardening of a Linux server. Installs and configures all open-source security tools, hardens SSH/kernel/firewall/permissions, runs all security scans, and generates a full report. Use when the user wants to secure a machine."
user-invocable: true
disable-model-invocation: true
allowed-tools: Bash, Read, Write, Edit, Grep, Glob, Agent, TaskCreate, TaskUpdate
argument-hint: "[full|scan-only|report]"
effort: max
---

# Linux Server Security Hardening Skill

You are performing a **complete security hardening** of a Linux server. Follow ALL phases below in order. Be systematic, thorough, and never skip a step.

## Arguments

- `$ARGUMENTS` can be:
  - **full** (default if empty): Run complete hardening + install tools + scan + report
  - **scan-only**: Skip hardening, only run all security scans and generate report
  - **report**: Only generate a status report of current security posture (no changes)

---

## PHASE 0: Reconnaissance

Before making ANY changes, gather system information:

```bash
# Run ALL of these and save the output mentally
cat /etc/os-release
uname -a
whoami
df -h /
free -h
nproc
systemctl list-units --type=service --state=running
ss -tlnp
iptables -L -n
cat /etc/ssh/sshd_config 2>/dev/null | grep -v '^#' | grep -v '^$'
```

Report the current state to the user before proceeding. Identify:
- OS and version
- Running services (especially SSH, web servers, databases)
- Open ports
- Current firewall state
- Any existing security tools

**IMPORTANT**: Identify services that MUST remain accessible (SSH, web server, etc.) and preserve their connectivity throughout the hardening process.

---

## PHASE 1: System Update

```bash
export DEBIAN_FRONTEND=noninteractive
apt-get update -qq
apt-get upgrade -y -qq
```

---

## PHASE 2: Install ALL Security Tools

Install in one batch to minimize time:

```bash
export DEBIAN_FRONTEND=noninteractive
apt-get install -y -qq \
  fail2ban ufw auditd audispd-plugins \
  rkhunter chkrootkit lynis \
  clamav clamav-daemon clamav-freshclam \
  aide \
  apparmor apparmor-utils apparmor-profiles apparmor-profiles-extra \
  libpam-pwquality libpam-tmpdir \
  acct sysstat \
  net-tools arpwatch \
  logwatch \
  needrestart debsums apt-show-versions \
  unattended-upgrades apt-listchanges \
  psad \
  firejail \
  openssh-server nmap nikto \
  tcpdump wireshark-common tshark \
  htop iotop iftop \
  secure-delete \
  gnupg2 \
  certbot \
  apt-transport-https ca-certificates curl software-properties-common
```

If `apt-listchanges` or `tiger` or `tripwire` fail, skip them and continue.

---

## PHASE 3: SSH Hardening

1. **Backup** existing config: `cp /etc/ssh/sshd_config /etc/ssh/sshd_config.backup.$(date +%Y%m%d)`
2. **Create** `/etc/ssh/sshd_config.d/hardening.conf` with the template from `${CLAUDE_SKILL_DIR}/templates/sshd_hardening.conf`
3. **Create** warning banners in `/etc/issue.net` and `/etc/issue`
4. **Validate**: `sshd -t` — if it fails, fix the config before proceeding
5. **Reload**: `systemctl reload ssh` (try `ssh` first, then `sshd` if not found)

**CRITICAL**: Never lock out the current SSH session. Keep `PermitRootLogin prohibit-password` (key-based root login). Do NOT change the SSH port unless the user explicitly asks.

---

## PHASE 4: Kernel Hardening (sysctl)

Create `/etc/sysctl.d/99-security-hardening.conf` with the template from `${CLAUDE_SKILL_DIR}/templates/sysctl_hardening.conf`

**IMPORTANT**: If Docker is running, do NOT disable `net.ipv4.ip_forward`. Comment it out with a note.

Apply with: `sysctl --system`
Verify key values: `sysctl net.ipv4.tcp_syncookies net.ipv4.conf.all.rp_filter kernel.randomize_va_space kernel.dmesg_restrict kernel.kptr_restrict`

---

## PHASE 5: Firewall (UFW)

```bash
ufw --force reset
ufw default deny incoming
ufw default allow outgoing
ufw limit ssh comment 'SSH rate-limited'
ufw allow 80/tcp comment 'HTTP'
ufw allow 443/tcp comment 'HTTPS'
ufw logging medium
ufw --force enable
```

**IMPORTANT**: If other services need public access (detected in Phase 0), add rules for them too. Always `limit` SSH, never just `allow`.

---

## PHASE 6: Fail2ban

Create `/etc/fail2ban/jail.local` with the template from `${CLAUDE_SKILL_DIR}/templates/fail2ban_jail.conf`

Then:
```bash
systemctl enable fail2ban
systemctl restart fail2ban
fail2ban-client status
```

If `sshd-ddos` filter doesn't exist, remove that jail from the config.

---

## PHASE 7: Auditd Rules

Create `/etc/audit/rules.d/hardening.rules` with the template from `${CLAUDE_SKILL_DIR}/templates/audit_rules.conf`

Load rules:
```bash
augenrules --load
auditctl -l | wc -l
```

---

## PHASE 8: Configure Security Tools

### ClamAV
```bash
systemctl stop clamav-freshclam
freshclam
systemctl enable clamav-freshclam && systemctl start clamav-freshclam
systemctl enable clamav-daemon && systemctl start clamav-daemon
```

### AIDE
```bash
aideinit  # Run in background - takes a long time
cp /var/lib/aide/aide.db.new /var/lib/aide/aide.db
```

### rkhunter
```bash
rkhunter --update
rkhunter --propupd
```
Create `/etc/rkhunter.conf.local`:
```
UPDATE_MIRRORS=1
MIRRORS_MODE=0
WEB_CMD=""
PKGMGR=DPKG
ALLOW_SSH_ROOT_USER=prohibit-password
ALLOW_SSH_PROT_V1=0
```

### PSAD
Configure hostname and enable auto-IDS in `/etc/psad/psad.conf`. If `ENABLE_AUTO_IDS_REGEX` is missing, add it.

### Process accounting & sysstat
```bash
systemctl enable acct && systemctl start acct
systemctl enable sysstat && systemctl start sysstat
```

---

## PHASE 9: File & Permission Hardening

### Critical file permissions
```bash
chmod 600 /etc/shadow /etc/gshadow
chmod 644 /etc/passwd /etc/group
chmod 600 /etc/ssh/sshd_config
chmod 700 /etc/ssh/sshd_config.d
chmod 600 /etc/crontab
chmod 700 /etc/cron.d /etc/cron.daily /etc/cron.weekly /etc/cron.monthly /etc/cron.hourly
chmod 600 /boot/grub/grub.cfg 2>/dev/null
chmod 700 /root
```

### Restrict cron and at
```bash
echo "root" > /etc/cron.allow
echo "root" > /etc/at.allow
chmod 600 /etc/cron.allow /etc/at.allow
```

### Disable core dumps
Create `/etc/security/limits.d/hardening.conf`:
```
* hard core 0
* soft core 0
```

### Password policy
Create `/etc/security/pwquality.conf` with strong settings (minlen=14, require upper/lower/digit/special).

### login.defs hardening
```
PASS_MAX_DAYS   90
PASS_MIN_DAYS   7
PASS_WARN_AGE   14
UMASK           027
LOGIN_RETRIES   3
LOGIN_TIMEOUT   60
```

### Disable unnecessary kernel modules
Create `/etc/modprobe.d/hardening.conf` to blacklist: dccp, sctp, rds, tipc, cramfs, freevxfs, jffs2, hfs, hfsplus, udf, and uncommon network protocols.

### Automatic security updates
Configure `/etc/apt/apt.conf.d/50unattended-upgrades` and `/etc/apt/apt.conf.d/20auto-upgrades`.

---

## PHASE 10: Web Server Hardening (if Nginx/Apache detected)

If Nginx is present:
1. Set `server_tokens off;` in `nginx.conf`
2. Remove TLSv1 and TLSv1.1, keep only TLSv1.2 and TLSv1.3
3. Set strong SSL ciphers
4. Create `/etc/nginx/conf.d/security-headers.conf` with:
   - X-Frame-Options: SAMEORIGIN
   - X-Content-Type-Options: nosniff
   - X-XSS-Protection: 1; mode=block
   - Referrer-Policy: strict-origin-when-cross-origin
   - Permissions-Policy
   - Content-Security-Policy
   - Strict-Transport-Security (HSTS)
5. Test with `nginx -t` and reload

If Certbot is installed and a domain is configured, offer to set up HTTPS.

---

## PHASE 11: Automated Scans (Cron)

Create `/etc/cron.d/security-scans`:
```
# ClamAV full scan weekly (Sunday 2 AM)
0 2 * * 0 root clamscan -r / --exclude-dir="^/sys" --exclude-dir="^/proc" --exclude-dir="^/dev" --exclude-dir="^/run" --quiet --infected --log=/var/log/clamav/weekly-scan.log

# rkhunter daily (3 AM)
0 3 * * * root /usr/bin/rkhunter --check --skip-keypress --quiet --report-warnings-only --logfile /var/log/rkhunter.log

# chkrootkit weekly (Monday 3:30 AM)
30 3 * * 1 root /usr/sbin/chkrootkit -q > /var/log/chkrootkit.log 2>&1

# Lynis monthly (1st, 4 AM)
0 4 1 * * root /usr/sbin/lynis audit system --cronjob --quiet > /var/log/lynis-audit.log 2>&1

# AIDE daily (5 AM)
0 5 * * * root /usr/bin/aide --check > /var/log/aide/aide-check.log 2>&1

# Security updates check (6 AM)
0 6 * * * root /usr/bin/apt-get update -qq && /usr/bin/apt-get -s upgrade 2>/dev/null | grep -i "security" > /var/log/security-updates.log

# Logwatch daily (7 AM)
0 7 * * * root /usr/sbin/logwatch --output file --filename /var/log/logwatch/daily-report.log --detail High
```

Create necessary log directories: `/var/log/aide`, `/var/log/logwatch`, `/var/log/clamav`.

---

## PHASE 12: Run ALL Security Scans

Run these scans **in parallel** where possible:

1. **Lynis**: `lynis audit system --cronjob`
2. **rkhunter**: `rkhunter --check --skip-keypress --report-warnings-only`
3. **chkrootkit**: `chkrootkit`
4. **ClamAV**: `clamscan -r /etc /usr/bin /usr/sbin /usr/local/bin /root /home /tmp --infected --quiet`
5. **Nmap**: `nmap -sV -sS -O --top-ports 1000 localhost`
6. **Nikto**: `nikto -h http://localhost -C all -nointeractive` (only if web server detected)
7. **debsums**: `debsums -s`
8. **John the Ripper**: Quick password audit on /etc/shadow

Wait for ALL scans to complete before generating the report.

---

## PHASE 13: Generate Security Report

Generate a comprehensive report with this exact structure:

```
# RAPPORT DE SECURITE COMPLET - [HOSTNAME]
**Date**: [DATE] | **OS**: [OS] | **Kernel**: [KERNEL]

## 1. LYNIS - Score: XX/100
- Warnings list
- Key suggestions

## 2. CLAMAV - Antivirus
- Malware found: X
- Suspicious items: X

## 3. RKHUNTER - Rootkits
- Rootkits found: X
- Warnings (with analysis of false positives)

## 4. CHKROOTKIT - Rootkits (2nd opinion)
- Rootkits found: X

## 5. NMAP - Port Scan
- Table of open ports with services

## 6. NIKTO - Web Vulnerabilities
- Findings

## 7. DEBSUMS - Package Integrity
- Corrupted packages

## 8. JOHN - Password Audit
- Weak passwords found

## 9. Network Audit
- Listening ports table
- Established connections

## 10. File Audit
- SUID/SGID files
- World-writable files
- Unowned files

## RESUME GLOBAL
Table with all categories, status, and scores.

## ACTIONS RECOMMANDEES
Prioritized list of remaining actions.
```

---

## Rules

- ALWAYS use `export DEBIAN_FRONTEND=noninteractive` before apt commands
- NEVER lock out SSH access
- ALWAYS backup configs before modifying them
- ALWAYS validate configs before reloading services (sshd -t, nginx -t, etc.)
- If Docker is running, preserve ip_forward and don't break Docker networking
- Run long scans (AIDE init, ClamAV) in background when possible
- Use TaskCreate/TaskUpdate to track progress through phases
- Report to the user at each phase completion
