# Claude Code Skill: `/hardening`

A comprehensive Linux server security hardening skill for [Claude Code](https://claude.ai/claude-code).

When invoked, Claude will automatically perform a **full security hardening** of your Linux server, install all major open-source security tools, and generate a detailed security report.

## Installation

Copy the skill into your Claude Code skills directory:

```bash
# Personal scope (available on all projects)
cp -r hardening ~/.claude/skills/

# Or project scope (available only in this project)
cp -r hardening .claude/skills/
```

## Usage

| Command | Description |
|---------|-------------|
| `/hardening` | Full hardening + tools installation + scans + report |
| `/hardening full` | Same as above |
| `/hardening scan-only` | Run all security scans and generate report (no changes) |
| `/hardening report` | Status report of current security posture (no changes) |

## What it does

### 13 Phases of Hardening

| Phase | Description |
|-------|-------------|
| 0 | **Reconnaissance** — Analyze current system state |
| 1 | **System Update** — `apt update && upgrade` |
| 2 | **Install Security Tools** — 30+ packages |
| 3 | **SSH Hardening** — Key-only auth, strong ciphers, disable forwarding |
| 4 | **Kernel Hardening** — 40+ sysctl security parameters |
| 5 | **Firewall (UFW)** — Deny-by-default, rate-limit SSH |
| 6 | **Fail2ban** — 7 jails (SSH + Nginx + recidive) |
| 7 | **Auditd** — 45+ audit rules for file/process monitoring |
| 8 | **Security Tools Config** — ClamAV, AIDE, rkhunter, PSAD |
| 9 | **File Hardening** — Permissions, password policy, core dumps, kernel modules |
| 10 | **Web Server Hardening** — Nginx security headers, TLS config |
| 11 | **Automated Scans** — Cron jobs for daily/weekly/monthly scans |
| 12 | **Run All Scans** — Lynis, ClamAV, rkhunter, chkrootkit, nmap, nikto, debsums |
| 13 | **Security Report** — Comprehensive report with findings and recommendations |

### Tools Installed

| Category | Tools |
|----------|-------|
| **Audit & IDS** | auditd, AIDE, PSAD, arpwatch |
| **Rootkit Detection** | rkhunter, chkrootkit |
| **Antivirus** | ClamAV (daemon + freshclam) |
| **Security Audit** | Lynis |
| **Brute-force Protection** | Fail2ban (7 jails) |
| **Firewall** | UFW |
| **MAC** | AppArmor |
| **Web Scanning** | Nikto, Nmap |
| **Password Audit** | John the Ripper |
| **Network Analysis** | tcpdump, tshark |
| **Sandboxing** | Firejail |
| **Integrity** | debsums, AIDE |
| **Monitoring** | Logwatch, sysstat, acct |
| **Auto-updates** | unattended-upgrades |

### Configuration Templates

Pre-configured templates included in `templates/`:

- `sshd_hardening.conf` — SSH hardening (Protocol 2, key-only, strong ciphers)
- `sysctl_hardening.conf` — Kernel parameters (ASLR, anti-spoofing, SYN protection)
- `fail2ban_jail.conf` — Fail2ban jails with progressive banning
- `audit_rules.conf` — 45 auditd rules (auth, sudo, cron, Docker, modules)
- `modprobe_hardening.conf` — Disabled kernel modules (dccp, sctp, cramfs, etc.)
- `nginx_security_headers.conf` — Security headers (XFO, HSTS, CSP, etc.)

## Supported Systems

- Ubuntu 22.04 / 24.04 LTS
- Debian 11 / 12

## Typical Lynis Score

Before: **~40-50/100** → After: **~80-85/100**

## License

MIT
