<p align="center">
  <h1 align="center">Claude Code Skill: <code>/hardening</code></h1>
  <p align="center">
    <strong>Automated Linux server hardening powered by AI</strong>
  </p>
  <p align="center">
    <a href="https://github.com/Cholulaa/claude-code-hardening-skill/stargazers"><img src="https://img.shields.io/github/stars/Cholulaa/claude-code-hardening-skill?style=for-the-badge&color=yellow" alt="Stars"></a>
    <a href="https://github.com/Cholulaa/claude-code-hardening-skill/network/members"><img src="https://img.shields.io/github/forks/Cholulaa/claude-code-hardening-skill?style=for-the-badge" alt="Forks"></a>
    <a href="https://github.com/Cholulaa/claude-code-hardening-skill/blob/main/LICENSE"><img src="https://img.shields.io/github/license/Cholulaa/claude-code-hardening-skill?style=for-the-badge" alt="License"></a>
    <a href="https://github.com/Cholulaa/claude-code-hardening-skill/issues"><img src="https://img.shields.io/github/issues/Cholulaa/claude-code-hardening-skill?style=for-the-badge" alt="Issues"></a>
  </p>
  <p align="center">
    <img src="https://img.shields.io/badge/CIS-Benchmark-blue?style=flat-square" alt="CIS">
    <img src="https://img.shields.io/badge/NIST-800--123-green?style=flat-square" alt="NIST">
    <img src="https://img.shields.io/badge/ANSSI-BP--028%20v2.0-red?style=flat-square" alt="ANSSI">
    <img src="https://img.shields.io/badge/DevSec-Framework-orange?style=flat-square" alt="DevSec">
    <img src="https://img.shields.io/badge/Ubuntu-22.04%20|%2024.04-E95420?style=flat-square&logo=ubuntu&logoColor=white" alt="Ubuntu">
    <img src="https://img.shields.io/badge/Debian-11%20|%2012-A81D33?style=flat-square&logo=debian&logoColor=white" alt="Debian">
  </p>
</p>

---

One command to harden your Linux server. Type `/hardening` in [Claude Code](https://claude.ai/claude-code) and let AI apply **CIS Benchmarks**, **NIST SP 800-123**, and **ANSSI BP-028** best practices — with **zero disruption** to your running services.

## Why This Skill?

| Problem | Solution |
|---------|----------|
| Server hardening takes hours of manual work | `/hardening` does it in minutes |
| Easy to miss critical security settings | 15 systematic phases, nothing skipped |
| Hardening scripts break running services | Smart service discovery asks before acting |
| One-size-fits-all doesn't work | 4 hardening levels from dev to military-grade |
| No proof of compliance | Generates CIS/NIST/ANSSI compliance report |
| Configuration drift after hardening | Automated daily/weekly security scans |

## Quick Start

```bash
# Install (30 seconds)
git clone https://github.com/Cholulaa/claude-code-hardening-skill.git ~/.claude/skills/hardening

# Use (in Claude Code)
/hardening              # Standard hardening
/hardening enhanced     # For production/compliance
/hardening scan-only    # Just scan, don't change anything
```

## 4 Hardening Levels

Inspired by [ANSSI BP-028 v2.0](https://cyber.gouv.fr) hardening tiers:

| Level | Time | Lynis Score | Risk | Best For |
|-------|------|-------------|------|----------|
| `minimal` | ~5 min | 60-65 | Low | Dev servers, temporary VMs |
| `standard` | ~15 min | 78-85 | Medium | Production servers, web apps |
| `enhanced` | ~25 min | 85-90 | High | Exposed servers, PCI-DSS/HIPAA |
| `paranoid` | ~35 min | 88-95 | Very High | Sensitive data, high-security |

## Smart Service Discovery

**This is what makes `/hardening` different.** Before touching anything, it:

1. **Scans** all running services, Docker containers, databases, web servers
2. **Maps** every listening port and active connection
3. **Analyzes** dependencies (Docker needs ip_forward, databases need localhost)
4. **Asks you** what must stay untouched
5. **Adapts** firewall rules and config to preserve your workloads
6. **Tests** each service after hardening — rolls back if broken

> No more "I hardened my server and everything broke."

## 15 Phases of Hardening

<details>
<summary><strong>Click to expand all phases</strong></summary>

| # | Phase | Level | Description |
|---|-------|-------|-------------|
| 0 | **Smart Recon** | ALL | Full service audit, impact analysis, user confirmation |
| 1 | **System Update** | ALL | `apt update && upgrade` |
| 2 | **Install Tools** | ALL | 30+ security packages (level-appropriate) |
| 3 | **SSH Hardening** | ALL | Key-only auth, strong ciphers, disable forwarding |
| 4 | **Kernel Hardening** | STD+ | 40+ sysctl params: ASLR, anti-spoofing, SYN protection |
| 5 | **Firewall (UFW)** | ALL | Deny-by-default, rate-limit SSH, service-aware rules |
| 6 | **Fail2ban** | ALL | Progressive banning, multi-service jails, recidive |
| 7 | **Auditd** | STD+ | 45+ rules: auth, sudo, cron, Docker, modules |
| 8 | **Security Tools** | STD+ | ClamAV, AIDE, rkhunter, PSAD, Logwatch config |
| 9 | **File Hardening** | ALL | Permissions, passwords, core dumps, kernel modules |
| 10 | **Systemd Sandboxing** | ENH+ | Per-service isolation with exposure scoring |
| 11 | **Web Server** | If detected | Headers, TLS 1.2+, CSP, HSTS |
| 12 | **Advanced** | PARANOID | GRUB password, kernel lockdown, USB protection |
| 13 | **Automated Scans** | STD+ | Cron: daily rkhunter, weekly ClamAV, monthly Lynis |
| 14 | **Run All Scans** | ALL | 11 parallel security scans |
| 15 | **Compliance Report** | ALL | CIS/NIST/ANSSI scoring and recommendations |

</details>

## 30+ Security Tools Installed

<details>
<summary><strong>Click to expand tool list</strong></summary>

| Category | Tools |
|----------|-------|
| **Audit & IDS** | auditd, AIDE, PSAD, arpwatch |
| **Rootkit Detection** | rkhunter, chkrootkit |
| **Antivirus** | ClamAV (daemon + auto-update) |
| **Security Audit** | Lynis, OpenSCAP (enhanced+) |
| **Brute-force Protection** | Fail2ban (7 jails) |
| **Firewall** | UFW (iptables frontend) |
| **MAC** | AppArmor (150+ profiles) |
| **Web Scanning** | Nikto, Nmap |
| **Password Audit** | John the Ripper |
| **Network Analysis** | tcpdump, tshark |
| **Sandboxing** | Firejail |
| **Package Integrity** | debsums |
| **Monitoring** | Logwatch, sysstat, acct |
| **Auto-updates** | unattended-upgrades |
| **Certificates** | Certbot (Let's Encrypt) |

</details>

## 7 Configuration Templates

| Template | What It Hardens |
|----------|----------------|
| `sshd_hardening.conf` | SSH: Protocol 2, key-only, ChaCha20/AES-GCM ciphers |
| `sysctl_hardening.conf` | Kernel: 40+ params (ASLR, rp_filter, SYN cookies) |
| `fail2ban_jail.conf` | Fail2ban: 7 jails with progressive banning |
| `audit_rules.conf` | Auditd: 45+ rules (auth, sudo, Docker, modules) |
| `modprobe_hardening.conf` | Kernel modules: disable 25+ protocols/filesystems |
| `nginx_security_headers.conf` | Web: XFO, HSTS, CSP, X-Content-Type, Referrer-Policy |
| `systemd_service_hardening.conf` | Systemd: 20+ sandboxing directives |

## Compliance Coverage

| Standard | Minimal | Standard | Enhanced | Paranoid |
|----------|---------|----------|----------|----------|
| **CIS Level 1** | ~50% | ~85% | ~90% | ~95% |
| **CIS Level 2** | ~30% | ~55% | ~70% | ~85% |
| **NIST 800-123** | ~45% | ~80% | ~88% | ~92% |
| **ANSSI Minimal** | ~95% | ~100% | ~100% | ~100% |
| **ANSSI Intermediary** | ~40% | ~85% | ~95% | ~100% |
| **ANSSI Enhanced** | ~20% | ~55% | ~75% | ~90% |
| **ANSSI High** | ~10% | ~35% | ~55% | ~75% |

## Safety Guarantees

- **Never locks out SSH** — your session is always preserved
- **Never disables services** without asking you first
- **Always backs up** configs before modifying them
- **Always validates** before reloading (`sshd -t`, `nginx -t`)
- **Tests services** after systemd hardening — rolls back if broken
- **Preserves Docker** networking when detected
- **Continues on failure** — one broken phase doesn't stop the rest

## Cross-Platform Compatibility

This skill uses the [Agent Skills open standard](https://www.anthropic.com/news/skills) and works with:
- **Claude Code** (CLI, Desktop, Web, IDE extensions)
- Any tool supporting the `SKILL.md` format

## Contributing

Contributions welcome! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

**Ideas for contributions:**
- Application-specific profiles (WordPress, PostgreSQL, Redis, etc.)
- Support for RHEL/Rocky/Alma Linux
- Wazuh/OSSEC integration
- Compliance report PDF generation
- Ansible playbook generation from hardening results

## References

- [CIS Benchmarks](https://www.cisecurity.org/cis-benchmarks) — Industry-standard security configuration
- [NIST SP 800-123](https://csrc.nist.gov/pubs/sp/800/123/final) — Guide to General Server Security
- [ANSSI BP-028 v2.0](https://cyber.gouv.fr) — French government Linux hardening guide
- [DevSec Hardening Framework](https://dev-sec.io/) — Security + DevOps automation
- [Madaidan's Linux Hardening](https://madaidans-insecurities.github.io/guides/linux-hardening.html) — Advanced hardening techniques
- [LinuxTeck Hardening Checklist](https://www.linuxteck.com/linux-server-hardening-checklist/) — 40+ essential steps

## License

[MIT](LICENSE) — Use it, fork it, improve it.

---

<p align="center">
  <sub>Built with Claude Code | If this helped secure your server, give it a star!</sub>
</p>
