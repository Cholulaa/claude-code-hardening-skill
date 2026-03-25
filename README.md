# Claude Code Skill: `/hardening`

A comprehensive Linux server security hardening skill for [Claude Code](https://claude.ai/claude-code), based on **CIS Benchmarks**, **NIST SP 800-123**, **ANSSI BP-028 v2.0**, and the **DevSec Hardening Framework**.

Features **smart service discovery** to avoid disrupting running applications, **4 hardening levels** from minimal to paranoid, **30+ security tools**, and **comprehensive compliance reporting**.

## Installation

```bash
# Personal scope (available on all projects)
git clone https://github.com/Cholulaa/claude-code-hardening-skill.git ~/.claude/skills/hardening

# Or project scope (available only in this project)
git clone https://github.com/Cholulaa/claude-code-hardening-skill.git .claude/skills/hardening
```

## Usage

| Command | Description |
|---------|-------------|
| `/hardening` | Standard hardening (default) |
| `/hardening minimal` | Essential baseline only — quick, low risk |
| `/hardening standard` | Full hardening with all tools (default) |
| `/hardening enhanced` | Aggressive hardening + systemd sandboxing + advanced kernel |
| `/hardening paranoid` | Maximum lockdown — kernel lockdown, restricted /proc, seccomp |
| `/hardening scan-only` | Run all security scans, generate report (no changes) |
| `/hardening report` | Status report of current posture (no changes) |

## Hardening Levels (inspired by ANSSI BP-028)

| Level | Based On | Impact | Use Case |
|-------|----------|--------|----------|
| **Minimal** | ANSSI Minimal | Low | Dev servers, temporary VMs |
| **Standard** | CIS Level 1 + ANSSI Intermediary | Medium | Production servers, web apps |
| **Enhanced** | CIS Level 2 + ANSSI Enhanced | High | Exposed servers, PCI-DSS/HIPAA compliance |
| **Paranoid** | ANSSI High + Madaidan's guide | Very High | Sensitive data, high-security environments |

## Smart Service Discovery

Before making ANY changes, the skill:

1. **Detects all running services** (web servers, databases, Docker, applications)
2. **Maps all listening ports** and active connections
3. **Identifies dependencies** (Docker needs ip_forward, databases need localhost ports)
4. **Asks the user** which services must remain untouched
5. **Adapts firewall rules** to preserve connectivity
6. **Tests services after hardening** and rolls back if broken

This ensures **zero disruption** to your production workloads.

## What It Does

### 15 Phases of Hardening

| Phase | Description | Level |
|-------|-------------|-------|
| 0 | **Smart Reconnaissance** — Full service audit and impact analysis | ALL |
| 1 | **System Update** — `apt update && upgrade` | ALL |
| 2 | **Install Security Tools** — 30+ packages (level-appropriate) | ALL |
| 3 | **SSH Hardening** — Key-only auth, strong ciphers, level-specific restrictions | ALL |
| 4 | **Kernel Hardening** — 40+ sysctl parameters, ASLR, anti-spoofing | STD+ |
| 5 | **Firewall (UFW)** — Deny-by-default, rate-limit SSH, service-aware rules | ALL |
| 6 | **Fail2ban** — Progressive banning, multi-service jails | ALL |
| 7 | **Auditd** — 45+ audit rules, privileged command monitoring | STD+ |
| 8 | **Security Tools Config** — ClamAV, AIDE, rkhunter, PSAD, Logwatch | STD+ |
| 9 | **File Hardening** — Permissions, password policy, core dumps, kernel modules, /proc | ALL |
| 10 | **Systemd Service Sandboxing** — Per-service isolation with exposure scoring | ENH+ |
| 11 | **Web Server Hardening** — Headers, TLS, application-specific security | If detected |
| 12 | **Advanced Hardening** — GRUB password, boot params, USB protection, kernel lockdown | PARANOID |
| 13 | **Automated Scans** — Cron jobs for daily/weekly/monthly scanning | STD+ |
| 14 | **Run All Scans** — 11 parallel security scans | ALL |
| 15 | **Compliance Report** — CIS/NIST/ANSSI compliance estimation | ALL |

### Security Tools Installed

| Category | Tools |
|----------|-------|
| **Audit & IDS** | auditd, AIDE, PSAD, arpwatch, osquery (enhanced+) |
| **Rootkit Detection** | rkhunter, chkrootkit |
| **Antivirus** | ClamAV (daemon + freshclam) |
| **Security Audit** | Lynis, OpenSCAP (enhanced+) |
| **Brute-force Protection** | Fail2ban (multi-jail) |
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

| Template | Description |
|----------|-------------|
| `sshd_hardening.conf` | SSH: Protocol 2, key-only, strong ciphers (ChaCha20/AES-GCM) |
| `sysctl_hardening.conf` | Kernel: 40+ security parameters (ASLR, anti-spoofing, SYN protection) |
| `fail2ban_jail.conf` | Fail2ban: Multi-jail with progressive banning |
| `audit_rules.conf` | Auditd: 45+ rules (auth, sudo, cron, Docker, modules) |
| `modprobe_hardening.conf` | Kernel modules: Disable 25+ unnecessary protocols/filesystems |
| `nginx_security_headers.conf` | Web: 7 security headers (XFO, HSTS, CSP, etc.) |
| `systemd_service_hardening.conf` | Systemd: 20+ sandboxing directives template |

## Compliance Standards Coverage

| Standard | Coverage |
|----------|----------|
| **CIS Benchmark Level 1** | ~85% (standard level) |
| **CIS Benchmark Level 2** | ~70% (enhanced level) |
| **NIST SP 800-123** | ~80% |
| **ANSSI BP-028 Minimal** | ~95% (minimal level) |
| **ANSSI BP-028 Intermediary** | ~85% (standard level) |
| **ANSSI BP-028 Enhanced** | ~75% (enhanced level) |

## Safety Guarantees

- **Never locks out SSH** — your session is always preserved
- **Never disables a service** without asking you first
- **Always backs up** configs before modifying them
- **Always validates** configs before reloading (sshd -t, nginx -t)
- **Always tests** services after systemd hardening — rolls back if broken
- **Preserves Docker** networking (ip_forward) when detected
- **Preserves database** localhost connectivity
- **Continues on failure** — if one phase fails, the next one runs

## Supported Systems

- Ubuntu 22.04 / 24.04 LTS
- Debian 11 / 12

## Typical Lynis Scores

| Level | Before | After |
|-------|--------|-------|
| Minimal | ~40-50 | ~60-65 |
| Standard | ~40-50 | ~78-85 |
| Enhanced | ~40-50 | ~85-90 |
| Paranoid | ~40-50 | ~88-95 |

## References

- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks)
- [NIST SP 800-123](https://csrc.nist.gov/pubs/sp/800/123/final)
- [ANSSI BP-028](https://cyber.gouv.fr)
- [DevSec Hardening Framework](https://dev-sec.io/)
- [Madaidan's Linux Hardening Guide](https://madaidans-insecurities.github.io/guides/linux-hardening.html)
- [LinuxTeck Hardening Checklist](https://www.linuxteck.com/linux-server-hardening-checklist/)

## License

MIT
