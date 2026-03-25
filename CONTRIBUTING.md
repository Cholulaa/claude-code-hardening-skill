# Contributing to `/hardening`

Thank you for your interest in improving this Claude Code skill! Contributions are welcome.

## How to Contribute

### Reporting Issues
- Use [GitHub Issues](https://github.com/Cholulaa/claude-code-hardening-skill/issues)
- Include your OS version, hardening level used, and error output
- For security vulnerabilities, see [SECURITY.md](SECURITY.md)

### Adding Features
1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Make your changes
4. Test on a fresh Ubuntu 22.04 or 24.04 VM
5. Submit a Pull Request

### What We're Looking For
- New hardening techniques (with CIS/NIST/ANSSI references)
- Application-specific profiles (WordPress, PostgreSQL, etc.)
- Support for additional distros (RHEL, Rocky, Alma)
- Bug fixes and compatibility improvements
- Translation of the README to other languages
- Better documentation and examples

### Code Style
- Templates should be well-commented
- Every hardening measure should reference its source (CIS control ID, NIST section, ANSSI recommendation)
- Safety first: never lock out SSH, always backup before modifying

## Testing
Before submitting a PR, test your changes:
```bash
# On a fresh VM
cp -r . ~/.claude/skills/hardening/
# Then invoke /hardening <level> in Claude Code
```

## Community
- Be respectful and constructive
- Help others in Issues and Discussions
- Share your Lynis score improvements!
