# Contributing to GitHub Actions Quality & Security Template

Thank you for considering contributing to this project! This document provides guidelines for contributing.

## 🤝 How to Contribute

### Reporting Issues

- Use the GitHub issue tracker
- Provide clear description of the issue
- Include workflow logs when relevant
- Specify language and versions

### Suggesting Features

- Open an issue with `[Feature Request]` prefix
- Describe the feature and use case
- Explain how it benefits users

### Pull Requests

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Test your changes thoroughly
5. Commit with clear messages (`git commit -m 'feat: add amazing feature'`)
6. Push to your fork (`git push origin feature/amazing-feature`)
7. Open a Pull Request

## 📝 Commit Message Convention

Follow [Conventional Commits](https://www.conventionalcommits.org/):

- `feat:` - New feature
- `fix:` - Bug fix
- `docs:` - Documentation changes
- `refactor:` - Code refactoring
- `test:` - Test additions/changes
- `chore:` - Maintenance tasks
- `security:` - Security improvements

Examples:
```
feat: add Go language support
fix: correct Python dependency detection
docs: update README with new examples
security: update pinned action SHAs
```

## 🔧 Development Guidelines

### Adding New Languages

1. Update `detect-language` job in `quality-gate.yml`
2. Add setup steps for the new language
3. Add lint, format, test, and build steps
4. Update `language-detect` composite action
5. Update documentation (agents.md, skills.md, README.md)
6. Add examples

### Adding New Security Tools

1. Add new job to `security-scan.yml`
2. Pin action with SHA256
3. Configure SARIF upload if applicable
4. Add skip flag input
5. Update documentation
6. Test with real projects

### Updating Pinned Actions

Use Dependabot or manually:

1. Check GitHub Marketplace for new version
2. Get the commit SHA for the new version
3. Update the `uses` line with new SHA
4. Update version comment
5. Test the workflow

Example:
```yaml
# Old
uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11 # v4.1.1

# New
uses: actions/checkout@<new-sha-here> # v4.2.0
```

## 🧪 Testing

### Local YAML Validation

```bash
# Install yamllint
pip install yamllint

# Validate workflows
yamllint -d relaxed .github/workflows/*.yml
yamllint -d relaxed .github/actions/*/action.yml
```

### Testing Workflows

1. Create a test repository
2. Copy workflows to `.github/workflows/`
3. Create sample project (Node.js, Python, or Java)
4. Trigger workflows and verify output
5. Check GitHub Security tab for SARIF uploads

### Testing Languages

**Node.js Test Project:**
```bash
npm init -y
npm install --save-dev eslint prettier jest
# Add lint, format, test scripts to package.json
```

**Python Test Project:**
```bash
python -m venv venv
pip install pytest black flake8
# Create tests/ directory with test files
```

**Java Test Project:**
```bash
# Create Maven or Gradle project
mvn archetype:generate -DgroupId=com.example -DartifactId=test
# Or
gradle init
```

## 📚 Documentation

When updating features:

1. Update `agents.md` with configuration details
2. Update `skills.md` with capability descriptions
3. Update `README.md` with usage examples
4. Update `IMPLEMENTATION.md` if architecture changes
5. Keep documentation in sync with code

## 🔒 Security

### Reporting Security Issues

**DO NOT** open public issues for security vulnerabilities.

Instead:
- Email maintainers privately
- Or use GitHub Security Advisory
- Provide detailed description
- Include steps to reproduce

### Security Requirements

All contributions must:
- Pin actions with SHA256
- Use minimum required permissions
- Include timeouts
- Not expose secrets
- Follow security best practices

## ✅ Pull Request Checklist

Before submitting:

- [ ] Code follows existing style
- [ ] Workflows are YAML valid
- [ ] Actions are pinned with SHA256
- [ ] Documentation is updated
- [ ] Examples are provided
- [ ] Tested with real project
- [ ] Commit messages follow convention
- [ ] No sensitive data committed

## 🎯 Areas for Contribution

### High Priority

- [ ] Additional language support (Go, Rust, PHP, Ruby)
- [ ] Matrix testing (multiple versions)
- [ ] Custom policy support
- [ ] Enhanced monorepo patterns
- [ ] Performance optimizations

### Medium Priority

- [ ] More security tools
- [ ] Additional formatters/linters
- [ ] Custom SBOM formats
- [ ] Enhanced license policies
- [ ] Integration examples

### Documentation

- [ ] Video tutorials
- [ ] More examples
- [ ] Migration guides
- [ ] Best practices guide
- [ ] FAQ section

## 💬 Communication

- GitHub Discussions for questions
- GitHub Issues for bugs/features
- Pull Requests for contributions

## 📜 License

By contributing, you agree that your contributions will be licensed under the MIT License.

## 🙏 Recognition

Contributors will be:
- Listed in CONTRIBUTORS.md
- Mentioned in release notes
- Credited in relevant documentation

## 📖 Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [Reusable Workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows)
- [Composite Actions](https://docs.github.com/en/actions/creating-actions/creating-a-composite-action)
- [SARIF Format](https://docs.github.com/en/code-security/code-scanning/integrating-with-code-scanning/sarif-support-for-code-scanning)
- [CodeQL](https://codeql.github.com/)

## 🎉 Thank You!

Your contributions help make this template better for everyone. We appreciate your time and effort!

---

**Questions?** Open a GitHub Discussion or create an issue.
