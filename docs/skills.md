# Skills and Capabilities Reference

## Overview

Este documento descreve as habilidades (skills) e capacidades técnicas implementadas no template de GitHub Actions para qualidade e segurança de código.

## Core Skills

### 1. Language Auto-Detection

**Capability**: Detectar automaticamente a linguagem/stack do projeto.

**Supported Languages**:
- **Node.js** (JavaScript/TypeScript)
- **Python**
- **Java**

**Detection Criteria**:

| Language | Detection Files |
|----------|----------------|
| Node.js | package.json, package-lock.json, yarn.lock, pnpm-lock.yaml, bun.lockb |
| Python | requirements.txt, setup.py, pyproject.toml, Pipfile, poetry.lock |
| Java | pom.xml, build.gradle, build.gradle.kts, gradlew, mvnw |

**Partial Support** (detected but limited tooling):
- Go (go.mod, go.sum)
- Rust (Cargo.toml)
- PHP (composer.json)

**Override**: Manual language specification via `language` input parameter.

---

### 2. Code Quality Analysis

#### 2.1 Linting

**Purpose**: Identificar problemas de código, bugs potenciais e anti-patterns.

**Tools by Language**:

##### Node.js
- **ESLint**: JavaScript/TypeScript linter
- **Execution**: `npm run lint` ou `npx eslint`
- **Configuration**: .eslintrc.*, eslint.config.js

##### Python
- **flake8**: Style guide enforcement (PEP 8)
- **pylint**: Static code analysis
- **mypy**: Type checking (optional)
- **Execution**: `flake8 .` e `pylint **/*.py`

##### Java
- **Checkstyle**: Code style enforcement
- **Execution**: `mvn checkstyle:check` ou `./gradlew check`
- **Configuration**: checkstyle.xml

**Output**: Relatórios de issues encontrados, falha em caso de erros críticos.

#### 2.2 Code Formatting

**Purpose**: Garantir formatação consistente do código.

**Tools by Language**:

##### Node.js
- **Prettier**: Opinionated code formatter
- **Execution**: `npm run format:check` ou `npx prettier --check`
- **Configuration**: .prettierrc, prettier.config.js

##### Python
- **black**: Uncompromising Python formatter
- **isort**: Import statement sorter
- **Execution**: `black --check .` e `isort --check-only .`
- **Configuration**: pyproject.toml

##### Java
- **Spotless**: Code formatter
- **Execution**: `mvn spotless:check` ou `./gradlew spotlessCheck`
- **Configuration**: pom.xml / build.gradle

**Output**: Lista de arquivos que precisam formatação, falha se inconsistências.

#### 2.3 Testing & Coverage

**Purpose**: Executar testes automatizados e medir cobertura de código.

**Tools by Language**:

##### Node.js
- **Jest**: Testing framework
- **Vitest**: Fast unit test framework
- **Coverage**: Built-in ou nyc/c8
- **Execution**: `npm run test:coverage` ou `npm test`
- **Threshold**: Configurável (default: 80%)

##### Python
- **pytest**: Testing framework
- **pytest-cov**: Coverage plugin
- **coverage**: Coverage measurement
- **Execution**: `pytest --cov=. --cov-report=xml`
- **Threshold**: Configurável (default: 80%)

##### Java
- **JUnit**: Testing framework
- **JaCoCo**: Code coverage
- **Execution**: `mvn test jacoco:report` ou `./gradlew test jacocoTestReport`
- **Threshold**: Configurável (default: 80%)

**Outputs**:
- Test results (pass/fail)
- Coverage reports (HTML, XML, terminal)
- Coverage artifacts uploaded for review

#### 2.4 Build

**Purpose**: Compilar/empacotar o projeto para verificar integridade.

**Process by Language**:

##### Node.js
- **Command**: `npm run build`
- **Output**: dist/, build/, ou out/
- **Common Tools**: webpack, rollup, vite, tsc, babel
- **Artifacts**: Bundled JavaScript/TypeScript

##### Python
- **Command**: `python -m build`
- **Output**: dist/ (wheels e sdist)
- **Tools**: build, setuptools, poetry
- **Artifacts**: .whl, .tar.gz

##### Java
- **Maven**: `mvn clean package -DskipTests`
- **Gradle**: `./gradlew build -x test`
- **Output**: target/*.jar ou build/libs/*.jar
- **Artifacts**: JAR/WAR files

**Artifacts**: Build outputs são armazenados com retenção de 7 dias.

---

### 3. Security Analysis (SAST)

#### 3.1 CodeQL

**Purpose**: Static Application Security Testing com análise semântica.

**Capabilities**:
- Detecção de vulnerabilidades de segurança
- Identificação de bugs e code smells
- Análise de fluxo de dados
- Queries: security-extended, security-and-quality

**Supported Languages**:
- JavaScript/TypeScript
- Python
- Java
- C/C++
- C#
- Go
- Ruby
- Swift

**Queries**:
- SQL Injection
- Cross-Site Scripting (XSS)
- Path Traversal
- Command Injection
- Insecure Deserialization
- Hardcoded Credentials
- Weak Cryptography
- etc.

**Output**: SARIF report uploaded to GitHub Security tab.

**Auto-detection**: Language automatically detected or specified via input.

---

### 4. Dependency Security (SCA)

#### 4.1 Dependency Review

**Purpose**: Software Composition Analysis - identificar dependências vulneráveis.

**Capabilities**:
- Compara dependências entre base e PR
- Identifica novas vulnerabilidades introduzidas
- Verifica licenças de dependências
- Bloqueia dependências com vulnerabilidades

**Severity Levels**:
- **Critical**: Bloqueia PR
- **High**: Bloqueia PR
- **Moderate**: Bloqueia PR
- **Low**: Warning apenas

**License Policy**:
- **Allowed**: MIT, Apache-2.0, BSD-2-Clause, BSD-3-Clause, ISC, 0BSD
- **Denied**: GPL-3.0, AGPL-3.0 (copyleft)

**Supported Ecosystems**:
- npm (Node.js)
- pip (Python)
- Maven (Java)
- E outros suportados pelo GitHub

**Limitation**: Funciona apenas em Pull Requests.

**Output**: Comentário no PR com análise detalhada.

---

### 5. Secret Scanning

#### 5.1 Gitleaks

**Purpose**: Detectar credenciais, tokens, chaves API expostas no código e histórico Git.

**Capabilities**:
- Varre todo o histórico do Git
- Detecta 100+ tipos de secrets
- Patterns customizáveis via .gitleaks.toml
- Entropia analysis para detectar secrets não padronizados

**Common Secrets Detected**:
- AWS Access Keys
- GitHub Tokens
- API Keys
- Private Keys (RSA, SSH)
- Database Connection Strings
- OAuth Tokens
- JWT Tokens
- Passwords em código

**Scan Scope**:
- Commits
- Branches
- Staged files
- Entire history (fetch-depth: 0)

**Output**:
- SARIF report
- GitHub Security tab alert
- Workflow failure se secrets encontrados

**Configuration**: .gitleaks.toml (opcional para customização).

---

### 6. Infrastructure as Code Security

#### 6.1 tfsec

**Purpose**: Scanner de segurança para Terraform.

**Capabilities**:
- Analisa Terraform code
- Detecta misconfigurations
- Best practices enforcement
- Cloud-specific checks (AWS, Azure, GCP)

**Check Categories**:
- Encryption
- Network exposure
- Access control
- Logging
- Backup policies

**Output**: SARIF report, GitHub Security tab.

#### 6.2 Checkov

**Purpose**: Scanner multi-framework para IaC.

**Supported Frameworks**:
- Terraform
- CloudFormation
- Kubernetes
- Helm
- Dockerfiles
- ARM Templates
- Ansible
- Serverless

**Capabilities**:
- 1000+ built-in policies
- CIS benchmarks
- Cloud best practices
- Custom policies support

**Output**: SARIF report, GitHub Security tab.

**Configuration**: Soft-fail mode para não bloquear por warnings.

---

### 7. Container Security

#### 7.1 Trivy

**Purpose**: Scanner de vulnerabilidades em containers.

**Capabilities**:
- OS package vulnerabilities
- Application dependency vulnerabilities
- Misconfigurations
- Secrets in images
- License detection

**Scan Types**:
- Image scanning
- Filesystem scanning
- Repository scanning

**Severity Detection**:
- CRITICAL
- HIGH
- MEDIUM
- LOW

**Databases**:
- NVD (National Vulnerability Database)
- GitHub Advisory Database
- OS-specific databases

**Process**:
1. Build Docker image
2. Scan with Trivy
3. Generate SARIF
4. Upload to Security tab

**Output**: SARIF report com CVEs encontrados.

**Requirement**: Dockerfile deve existir no caminho especificado.

---

### 8. SBOM Generation

#### 8.1 Syft

**Purpose**: Gerar Software Bill of Materials.

**Capabilities**:
- Inventory de todos os componentes
- Dependências diretas e transitivas
- Versões e licenças
- Package URLs (purl)

**Formats**:
- **CycloneDX JSON**: Industry standard
- **SPDX JSON**: Linux Foundation standard

**Supported Ecosystems**:
- npm, yarn, pnpm (Node.js)
- pip, poetry (Python)
- Maven, Gradle (Java)
- Go modules
- Rust cargo
- Ruby gems
- E muitos outros

**Use Cases**:
- Supply chain security
- Compliance auditing
- Vulnerability tracking
- License compliance

**Output**:
- sbom.cyclonedx.json
- sbom.spdx.json
- Retention: 30 days

**Integration**: Pode ser usado com Grype para vulnerability scanning.

---

### 9. License Compliance

#### 9.1 Node.js License Checking

**Tool**: license-checker

**Capabilities**:
- Lista todas as licenças de dependências npm
- Identifica licenças incompatíveis
- Gera relatórios JSON e CSV

**Output**: licenses.json com licenças de cada dependência.

#### 9.2 Python License Checking

**Tool**: pip-licenses

**Capabilities**:
- Lista licenças de pacotes Python
- Detecta licenças desconhecidas
- Suporta múltiplos formatos

**Output**: python-licenses.json.

**Policy Enforcement**:
- Compatible: MIT, Apache-2.0, BSD variants, ISC
- Incompatible: GPL-3.0, AGPL-3.0 (require disclosure)

**Manual Review**: Relatórios disponíveis como artifacts para revisão.

---

## Advanced Skills

### 10. Concurrency Control

**Purpose**: Otimizar uso de runners e prevenir execuções redundantes.

**Strategy**:
```yaml
concurrency:
  group: quality-${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

**Benefits**:
- Cancela workflows antigos quando novo push é feito
- Economiza minutos de runner
- Feedback mais rápido

**Groups**:
- quality-*: Para workflows de qualidade
- security-*: Para workflows de segurança
- Por branch/PR/tag

---

### 11. Artifact Management

**Purpose**: Armazenar outputs de builds, coverage, SBOM para análise.

**Retention Policies**:
- **Build artifacts**: 7 dias
- **Coverage reports**: 7 dias
- **SBOM**: 30 dias (compliance)
- **License reports**: 30 dias

**Artifact Types**:
- Build outputs (dist/, target/, build/)
- Coverage (coverage/, htmlcov/, jacoco/)
- SBOM (cyclonedx.json, spdx.json)
- License reports (licenses.json)

**Access**: Via GitHub UI ou API.

---

### 12. SARIF Integration

**Purpose**: Formato padronizado para resultados de segurança.

**Tools Generating SARIF**:
- CodeQL
- Trivy
- tfsec
- Checkov
- Gitleaks (via custom config)

**Benefits**:
- Visualização unificada no GitHub Security tab
- Code scanning alerts
- Integration com GitHub Advanced Security

**Upload**: Automático via `codeql-action/upload-sarif`.

---

### 13. Minimum Privilege

**Purpose**: Aplicar princípio de menor privilégio.

**Permissions by Job**:

```yaml
# Read-only por padrão
permissions:
  contents: read

# Quality jobs
permissions:
  contents: read
  pull-requests: write  # Para comentários de coverage

# Security jobs
permissions:
  contents: read
  security-events: write  # Para upload SARIF
  pull-requests: write  # Para dependency review
```

**Benefits**:
- Reduz superfície de ataque
- Limita impacto de tokens comprometidos
- Compliance com security best practices

---

### 14. Pinned Actions

**Purpose**: Prevenir supply chain attacks.

**Strategy**: Todas as actions são pinadas com SHA256:

```yaml
uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11 # v4.1.1
```

**Benefits**:
- Imutabilidade
- Proteção contra malicious updates
- Auditabilidade

**Maintenance**: Periodic reviews e updates via Dependabot ou Renovate.

---

### 15. Timeout Protection

**Purpose**: Prevenir workflows travados.

**Configuration**:
- Default: 30 minutos (quality)
- Security: 45 minutos
- Per-job: Configurável via input

**Benefits**:
- Economiza minutos de runner
- Detecta problemas rapidamente
- Previne deadlocks

---

### 16. Cache Management

**Purpose**: Acelerar builds com cache de dependências.

**Caches**:
- npm cache (Node.js)
- pip cache (Python)
- Maven cache (Java)
- Gradle cache (Java)

**Auto-management**: Actions de setup fazem cache automaticamente.

**Invalidation**: Por hash de lock files (package-lock.json, requirements.txt).

---

## Integration Skills

### 17. Monorepo Support

**Capability**: Suporta projetos com múltiplas linguagens/módulos.

**Strategy**: Usar `working-directory` input:

```yaml
frontend-quality:
  uses: ./.github/workflows/quality-gate.yml
  with:
    working-directory: './frontend'
    language: nodejs

backend-quality:
  uses: ./.github/workflows/quality-gate.yml
  with:
    working-directory: './backend'
    language: python
```

---

### 18. Matrix Testing

**Future Capability**: Testar múltiplas versões de runtime.

**Example** (não implementado no template base):
```yaml
strategy:
  matrix:
    node-version: [18, 20, 22]
    os: [ubuntu-latest, windows-latest]
```

---

### 19. Conditional Execution

**Capability**: Pular jobs baseado em condições.

**Skip Flags**:
- `skip-lint`
- `skip-format`
- `skip-test`
- `skip-build`
- `skip-codeql`
- `skip-secrets`
- `skip-iac`
- `skip-container`
- `skip-sbom`
- `skip-license`

**Auto-skip**:
- dependency-review: Apenas em PRs
- container-scanning: Apenas se Dockerfile existe

---

### 20. Quality Gates

**Capability**: Bloquear merges baseado em critérios.

**Criteria**:
- Testes devem passar
- Coverage >= threshold (configurável)
- Lint sem erros
- Format check passa
- Build sucede
- Sem vulnerabilidades críticas (dependency-review)
- Sem secrets expostos (gitleaks)

**Enforcement**: Via branch protection rules.

---

## Skill Matrix

| Skill | Node.js | Python | Java | Output |
|-------|---------|--------|------|--------|
| Language Detection | ✅ | ✅ | ✅ | Console |
| Linting | ✅ ESLint | ✅ flake8/pylint | ✅ Checkstyle | Console |
| Formatting | ✅ Prettier | ✅ black/isort | ✅ Spotless | Console |
| Testing | ✅ Jest | ✅ pytest | ✅ JUnit | Console + Artifact |
| Coverage | ✅ nyc/c8 | ✅ coverage | ✅ JaCoCo | Artifact |
| Build | ✅ webpack/vite | ✅ build | ✅ Maven/Gradle | Artifact |
| SAST (CodeQL) | ✅ | ✅ | ✅ | SARIF |
| SCA (Dependencies) | ✅ | ✅ | ✅ | PR Comment |
| Secret Scanning | ✅ | ✅ | ✅ | SARIF |
| IaC Scanning | ✅ | ✅ | ✅ | SARIF |
| Container Scanning | ✅ | ✅ | ✅ | SARIF |
| SBOM | ✅ | ✅ | ✅ | Artifact |
| License Check | ✅ | ✅ | ⚠️ | Artifact |

**Legend**: ✅ Full Support, ⚠️ Partial Support

---

## Performance Characteristics

### Execution Time (Typical)

| Job | Duration | Can Skip? |
|-----|----------|-----------|
| Language Detection | 30s | No |
| Lint | 1-3 min | Yes |
| Format Check | 30s-2 min | Yes |
| Test + Coverage | 2-10 min | Yes |
| Build | 1-5 min | Yes |
| CodeQL | 5-15 min | Yes |
| Dependency Review | 30s-2 min | No (PR only) |
| Secret Scan | 1-3 min | Yes |
| IaC Scan | 2-5 min | Yes |
| Container Scan | 3-8 min | Yes |
| SBOM | 1-3 min | Yes |
| License Check | 1-2 min | Yes |

**Total (Full Suite)**: ~15-45 minutos
**Optimized (Skip non-critical)**: ~5-15 minutos

---

## Extensibility

### Adding New Languages

1. Update `detect-language` step
2. Add setup step for new language
3. Add tool-specific commands
4. Update documentation

### Adding New Tools

1. Add new step to appropriate job
2. Pin action with SHA
3. Configure SARIF upload if applicable
4. Add skip flag if optional
5. Document in agents.md

### Custom Policies

- tfsec: .tfsec directory
- Checkov: .checkov.yml
- Gitleaks: .gitleaks.toml
- ESLint: .eslintrc
- Prettier: .prettierrc

---

## Monitoring & Observability

### GitHub Actions Insights

- Workflow run history
- Job duration trends
- Success/failure rates
- Artifact storage usage

### Security Dashboard

- GitHub Security tab
- Code scanning alerts
- Dependabot alerts
- Secret scanning alerts

### Notifications

- PR comments (dependency-review)
- Email notifications (workflow failures)
- GitHub notifications
- Slack/Teams integration (via webhooks)

---

## Compliance & Standards

### Standards Addressed

- **OWASP Top 10**: Via CodeQL SAST
- **CIS Benchmarks**: Via Checkov
- **PCI DSS**: Secret scanning, encryption checks
- **GDPR**: Data protection scanning
- **SOC 2**: Audit trails, SBOM

### Certifications Supported

- ISO 27001: Security controls
- SOC 2 Type II: Continuous monitoring
- HIPAA: Security scanning
- FedRAMP: Security baselines

---

## Continuous Improvement

### Metrics to Track

- Code coverage trends
- Vulnerability discovery rate
- Mean time to remediation
- False positive rate
- Workflow execution time

### Optimization Strategies

1. Cache dependencies aggressively
2. Skip redundant jobs
3. Parallelize independent jobs
4. Use conditional execution
5. Incremental builds

---

## Support & Resources

### Documentation
- agents.md: Workflow configuration
- skills.md: This document
- README.md: Quick start guide

### External Resources
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [CodeQL Documentation](https://codeql.github.com/docs/)
- [Trivy Documentation](https://aquasecurity.github.io/trivy/)
- [Checkov Documentation](https://www.checkov.io/)

### Community
- GitHub Discussions
- Stack Overflow
- Security community forums

---

## Version History

- **v1.0**: Initial implementation with Node.js, Python, Java support
- Security scanning: CodeQL, Gitleaks, Trivy, tfsec, Checkov
- SBOM generation with Syft
- License compliance checking
