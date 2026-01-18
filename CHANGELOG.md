# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added - Marketplace Publishing Support

#### Root Action for Marketplace
- **Root action.yml** - Primary composite action at repository root
  - Enables GitHub Marketplace publishing
  - Provides unified interface for quality and security scanning
  - Supports three modes: `quality`, `security`, `all`
  - Auto-detects project language (Node.js, Python, Java)
  - Delegates to existing sub-actions for functionality
  - Comprehensive inputs for both quality and security options
  - Outputs: detected-language, quality-passed, security-completed
  - Branding: shield icon with blue color
  - ~130 lines of marketplace-ready action definition

#### New Example Workflows
- **All Usage Patterns Example** (`all-usage-patterns.yml`)
  - Demonstrates 5 different usage patterns
  - Pattern 1: Marketplace action (root) with all modes
  - Pattern 2: Reusable workflows (quality-gate, security-scan)
  - Pattern 3: Individual actions via local path
  - Pattern 4: Individual actions via remote path
  - Pattern 5: Custom combination of patterns
  - Complete examples for each scenario
  - ~210 lines of comprehensive examples

#### Documentation Updates
- **README.md enhancements**
  - Added "Opção 1: Usar Action Publicada no Marketplace" section
  - Documented three main usage options
  - Added marketplace publishing guide
  - Included path reference documentation
  - Added comparison table of usage methods
  - Explained which methods are marketplace-publishable
  - Updated all examples to show marketplace and path usage
  - Added semantic versioning recommendations

### Changed

#### Repository Structure
- Actions in subfolders remain accessible via path reference
- Root action serves as marketplace entry point
- All existing workflows remain functional
- Backwards compatible with existing usage patterns

#### Usage Patterns
- Three distinct usage patterns now available:
  1. **Marketplace**: `owner/repo@v1` (publishable)
  2. **Path Reference**: `owner/repo/.github/actions/name@v1` (usable, not publishable)
  3. **Workflow Call**: `owner/repo/.github/workflows/name.yml@v1` (usable, not publishable)

### Technical Details

#### Why This Structure?
- GitHub Marketplace requires `action.yml` at repository root
- Actions in subfolders cannot be published to marketplace
- But they CAN be used via path reference from any workflow
- This structure provides both marketplace publishing AND modular usage

#### Path Reference Support
- Language detect: `owner/repo/.github/actions/language-detect@v1`
- Security scan: `owner/repo/.github/actions/security-scan@v1`
- Works from external repositories
- Works from local repository (after clone/submodule)

## [1.0.0] - 2024-01-18

### Added

#### Workflows
- **Quality Gate Workflow** (`quality-gate.yml`)
  - Auto-detection for Node.js, Python, and Java projects
  - Lint job with ESLint, flake8/pylint, Checkstyle
  - Format check with Prettier, black/isort, Spotless
  - Test execution with coverage tracking (Jest, pytest, JUnit)
  - Build job with npm, python build, Maven/Gradle
  - Configurable inputs (skip flags, thresholds, timeouts)
  - 483 lines of production-ready workflow code

- **Security Scan Workflow** (`security-scan.yml`)
  - SAST with CodeQL (security-extended queries)
  - SCA with Dependency Review
  - Secret scanning with Gitleaks
  - IaC scanning with tfsec and Checkov
  - Container scanning with Trivy
  - SBOM generation with Syft (CycloneDX + SPDX)
  - License compliance checking
  - 336 lines of comprehensive security scanning

- **Example Usage Workflow** (`example-usage.yml`)
  - Complete usage example
  - Demonstrates quality + security integration
  - 48 lines with clear comments

#### Composite Actions
- **Language Detection Action** (`language-detect`)
  - Auto-detects Node.js, Python, Java
  - Outputs: detected-language, has-nodejs, has-python, has-java
  - Override capability via inputs
  - 91 lines of smart detection logic

- **Security Scan Action** (`security-scan`)
  - Placeholder for consolidated security scanning
  - Extensible design
  - 55 lines

#### Documentation
- **agents.md** (434 lines)
  - Complete agent configuration guide
  - Input/output specifications
  - Usage examples for all languages
  - Integration patterns
  - Security best practices
  - Troubleshooting guide

- **skills.md** (798 lines)
  - Comprehensive capabilities reference
  - Tool descriptions and features
  - Language support matrix
  - Performance characteristics
  - Compliance standards
  - Metrics and monitoring

- **README.md** (401 lines)
  - Quick start guide
  - Feature highlights
  - Usage examples
  - Customization guide
  - Badge templates
  - Support information

- **IMPLEMENTATION.md** (297 lines)
  - Detailed implementation summary
  - Requirements checklist
  - Deliverables overview
  - Security features
  - Architecture explanation

- **CONTRIBUTING.md** (150+ lines)
  - Contribution guidelines
  - Development setup
  - Testing procedures
  - Security reporting

- **CHANGELOG.md** (this file)
  - Version history
  - Change tracking

#### Security Features
- All 13 actions pinned with SHA256 commits
- Minimum privilege (RBAC) on all jobs
- Configurable timeouts (30/45 minutes default)
- Concurrency control (cancel-in-progress)
- SARIF upload support for all security tools
- Artifact management with retention policies
- persist-credentials: false for security

#### Tools Integrated
- **Linting**: ESLint, flake8, pylint, Checkstyle
- **Formatting**: Prettier, black, isort, Spotless
- **Testing**: Jest, pytest, JUnit
- **Coverage**: nyc/c8, coverage, JaCoCo
- **SAST**: CodeQL
- **SCA**: Dependency Review
- **Secrets**: Gitleaks
- **IaC**: tfsec, Checkov
- **Container**: Trivy
- **SBOM**: Syft
- **License**: license-checker, pip-licenses

#### Project Files
- `.gitignore` - Comprehensive ignore patterns
- `LICENSE` - MIT License
- Repository structure with `.github/` directory

### Language Support
- ✅ Node.js (JavaScript/TypeScript)
- ✅ Python
- ✅ Java

### Security
- 9 security tools integrated
- SARIF format for unified reporting
- GitHub Security tab integration
- SBOM generation (CycloneDX, SPDX)
- License compliance checking

### Best Practices
- Reusable workflows (workflow_call)
- Composite actions
- Auto-detection with manual override
- Comprehensive error handling
- Configurable via inputs
- Well-documented

## [Unreleased]

### Planned Features
- Go language support
- Rust language support
- PHP language support
- Ruby language support
- Matrix testing (multiple versions)
- Custom policy configurations
- Enhanced monorepo support
- Performance optimizations

### Future Security Tools
- Additional SAST tools
- Dependency confusion detection
- API security testing
- Fuzzing integration

### Documentation Improvements
- Video tutorials
- Migration guides
- More examples
- FAQ section

## Release Notes

### v1.0.0 - Initial Release

This is the initial production-ready release of the GitHub Actions Quality & Security Template.

**Highlights:**
- 🎯 Complete quality and security pipeline
- 🔍 Auto-detection for 3 major languages
- 🛡️ 9 security tools integrated
- 📚 1,633 lines of documentation
- ✅ Production-ready and validated
- 🔒 Security best practices implemented

**Total Lines of Code:** 2,646+

**Ready for:** Immediate production use in Node.js, Python, and Java projects.

---

## Versioning

This project follows [Semantic Versioning](https://semver.org/):
- **MAJOR**: Incompatible API changes
- **MINOR**: Backwards-compatible new features
- **PATCH**: Backwards-compatible bug fixes

## Links

- [Repository](https://github.com/celfons/agents-default-actions-template)
- [Documentation](docs/)
- [Issues](https://github.com/celfons/agents-default-actions-template/issues)
- [Discussions](https://github.com/celfons/agents-default-actions-template/discussions)
