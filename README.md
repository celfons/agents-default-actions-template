# GitHub Actions Quality & Security Template

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![GitHub Actions](https://img.shields.io/badge/GitHub-Actions-2088FF?logo=github-actions&logoColor=white)](https://github.com/features/actions)

Template agnóstico de stack para padronização de **QUALIDADE** e **SEGURANÇA** em projetos de software usando GitHub Actions.

## 🎯 Objetivo

Fornecer workflows reutilizáveis (`workflow_call`) e composite actions que implementam as melhores práticas de:
- **Qualidade de Código**: Lint, formatação, testes com cobertura, build
- **Segurança**: SAST, SCA, secrets, IaC, containers, SBOM, licenças

## ✨ Características

### 🔍 Auto-detecção de Linguagens
- **Node.js** (JavaScript/TypeScript)
- **Python**
- **Java**
- Override manual via inputs

### ✅ Quality Gate
- **Lint**: ESLint, flake8/pylint, Checkstyle
- **Format**: Prettier, black/isort, Spotless
- **Tests**: Jest, pytest, JUnit (com cobertura)
- **Build**: webpack/vite, python build, Maven/Gradle

### 🔒 Security Scanning
- **SAST**: CodeQL (GitHub)
- **SCA**: Dependency Review
- **Secrets**: Gitleaks
- **IaC**: tfsec, Checkov
- **Containers**: Trivy
- **SBOM**: Syft (CycloneDX, SPDX)
- **Licenses**: license-checker, pip-licenses

### 🛡️ Best Practices
- ✅ Actions pinadas com SHA256
- ✅ Mínimos privilégios (RBAC)
- ✅ Timeouts configurados
- ✅ Concurrency control
- ✅ SARIF upload (GitHub Security)
- ✅ Artifact management
- ✅ Persist-credentials: false

## 🚀 Quick Start

### 1. Instalação

Copie os workflows para seu repositório:

```bash
# Copie a estrutura de .github
cp -r .github /path/to/your/repo/

# Ou adicione como submódulo
git submodule add https://github.com/celfons/agents-default-actions-template .github/templates
```

### 2. Uso Básico

Crie `.github/workflows/ci.yml` no seu repositório:

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

permissions:
  contents: read

jobs:
  quality:
    uses: ./.github/workflows/quality-gate.yml
    permissions:
      contents: read
      pull-requests: write
    with:
      language: auto  # ou nodejs, python, java
      coverage-threshold: 80

  security:
    uses: ./.github/workflows/security-scan.yml
    permissions:
      contents: read
      security-events: write
      pull-requests: write
    secrets: inherit
```

### 3. Customização

#### Node.js Project

```yaml
jobs:
  quality:
    uses: ./.github/workflows/quality-gate.yml
    with:
      language: nodejs
      skip-lint: false
      skip-format: false
      skip-test: false
      coverage-threshold: 90
```

#### Python Project

```yaml
jobs:
  quality:
    uses: ./.github/workflows/quality-gate.yml
    with:
      language: python
      coverage-threshold: 85
      working-directory: './src'
```

#### Java Project

```yaml
jobs:
  quality:
    uses: ./.github/workflows/quality-gate.yml
    with:
      language: java
      timeout-minutes: 45
```

#### Monorepo

```yaml
jobs:
  frontend:
    uses: ./.github/workflows/quality-gate.yml
    with:
      language: nodejs
      working-directory: './frontend'
  
  backend:
    uses: ./.github/workflows/quality-gate.yml
    with:
      language: python
      working-directory: './backend'
```

## 📋 Workflows Disponíveis

### Quality Gate (`quality-gate.yml`)

Pipeline de qualidade com auto-detecção de linguagem.

**Jobs**:
1. `detect-language` - Detecta a linguagem do projeto
2. `lint` - Executa linters (ESLint, flake8, Checkstyle)
3. `format-check` - Verifica formatação (Prettier, black, Spotless)
4. `test` - Executa testes com cobertura
5. `build` - Compila/empacota o projeto

**Inputs**:
| Input | Descrição | Default |
|-------|-----------|---------|
| `language` | Linguagem (auto, nodejs, python, java) | `auto` |
| `skip-lint` | Pular lint | `false` |
| `skip-format` | Pular format check | `false` |
| `skip-test` | Pular testes | `false` |
| `skip-build` | Pular build | `false` |
| `coverage-threshold` | Cobertura mínima (%) | `80` |
| `working-directory` | Diretório de trabalho | `.` |
| `timeout-minutes` | Timeout do job | `30` |

### Security Scan (`security-scan.yml`)

Pipeline de segurança completo.

**Jobs**:
1. `codeql-analysis` - SAST com CodeQL
2. `dependency-review` - SCA (vulnerabilidades em dependências)
3. `secret-scanning` - Detecção de secrets (Gitleaks)
4. `iac-scanning` - Segurança de IaC (tfsec, Checkov)
5. `container-scanning` - Segurança de containers (Trivy)
6. `sbom-generation` - Geração de SBOM (Syft)
7. `license-check` - Verificação de licenças

**Inputs**:
| Input | Descrição | Default |
|-------|-----------|---------|
| `language` | Linguagem para CodeQL | `auto` |
| `skip-codeql` | Pular CodeQL | `false` |
| `skip-dependency-review` | Pular dependency review | `false` |
| `skip-secrets` | Pular secret scanning | `false` |
| `skip-iac` | Pular IaC scanning | `false` |
| `skip-container` | Pular container scanning | `false` |
| `skip-sbom` | Pular SBOM | `false` |
| `skip-license` | Pular license check | `false` |
| `dockerfile-path` | Caminho do Dockerfile | `./Dockerfile` |
| `timeout-minutes` | Timeout do job | `45` |

## 🧩 Composite Actions

### Language Detect (`language-detect`)

Detecta automaticamente a linguagem do projeto.

```yaml
- uses: ./.github/actions/language-detect
  id: detect
  with:
    override-language: auto

- run: echo "Language: ${{ steps.detect.outputs.detected-language }}"
```

**Outputs**:
- `detected-language` - Linguagem detectada
- `has-nodejs` - Tem Node.js?
- `has-python` - Tem Python?
- `has-java` - Tem Java?

### Security Scan (`security-scan`)

Action placeholder para varredura consolidada.

```yaml
- uses: ./.github/actions/security-scan
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    language: auto
```

## 📚 Documentação

- **[agents.md](docs/agents.md)**: Guia completo de configuração dos workflows
- **[skills.md](docs/skills.md)**: Referência de skills e capacidades
- **[example-usage.yml](.github/workflows/example-usage.yml)**: Exemplo de uso

## 🔧 Requisitos

### Repositório

- GitHub Actions habilitado
- Branch protection configurado (opcional, mas recomendado)

### Projetos Node.js

- `package.json` com scripts:
  ```json
  {
    "scripts": {
      "lint": "eslint .",
      "format:check": "prettier --check .",
      "test": "jest",
      "test:coverage": "jest --coverage",
      "build": "webpack"
    }
  }
  ```

### Projetos Python

- `requirements.txt` ou `pyproject.toml`
- Testes com pytest
- Opcional: `requirements-dev.txt` com ferramentas de desenvolvimento

### Projetos Java

- `pom.xml` (Maven) ou `build.gradle` (Gradle)
- JUnit configurado
- Opcional: Checkstyle, Spotless

## 🔐 Segurança

### Actions Pinadas

Todas as actions são pinadas com SHA256:

```yaml
uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11 # v4.1.1
```

### Permissões Mínimas

```yaml
permissions:
  contents: read
  security-events: write
```

### SARIF Reports

Resultados de segurança são enviados para o GitHub Security tab:
- CodeQL
- Trivy
- tfsec
- Checkov

## 📊 Artifacts

Os workflows geram os seguintes artifacts:

| Artifact | Retenção | Descrição |
|----------|----------|-----------|
| `coverage-*` | 7 dias | Relatórios de cobertura |
| `build-*` | 7 dias | Outputs de build |
| `sbom.*` | 30 dias | Software Bill of Materials |
| `license-reports` | 30 dias | Relatórios de licenças |

## ⚙️ Configuração Avançada

### Quality Gate com Threshold

```yaml
quality:
  uses: ./.github/workflows/quality-gate.yml
  with:
    coverage-threshold: 90
    timeout-minutes: 45
```

### Security Scan Seletivo

```yaml
security:
  uses: ./.github/workflows/security-scan.yml
  with:
    skip-codeql: false
    skip-secrets: false
    skip-container: true  # Sem Dockerfile
```

### Branch Protection

Configure branch protection rules:
- ✅ Require status checks: `quality`, `security`
- ✅ Require pull request reviews
- ✅ Require conversation resolution

## 🎨 Badges

Adicione badges ao seu README:

```markdown
[![Quality Gate](https://github.com/seu-usuario/seu-repo/actions/workflows/quality-gate.yml/badge.svg)](https://github.com/seu-usuario/seu-repo/actions/workflows/quality-gate.yml)
[![Security Scan](https://github.com/seu-usuario/seu-repo/actions/workflows/security-scan.yml/badge.svg)](https://github.com/seu-usuario/seu-repo/actions/workflows/security-scan.yml)
```

## 🐛 Troubleshooting

### Linguagem não detectada
- Verifique se os arquivos de manifesto existem
- Use `language: nodejs|python|java` para forçar

### Testes falhando
- Node.js: Certifique-se de ter script `test` no package.json
- Python: Instale pytest
- Java: Configure JUnit

### CodeQL timeout
- Aumente `timeout-minutes`
- Para Java/C++, pode ser necessário customizar build

### Container scan pulado
- Verifique se Dockerfile existe
- Use `dockerfile-path` para especificar caminho

## 🤝 Contribuindo

Contribuições são bem-vindas! Por favor:

1. Fork o repositório
2. Crie uma branch (`git checkout -b feature/nova-feature`)
3. Commit suas mudanças (`git commit -am 'Add nova feature'`)
4. Push para a branch (`git push origin feature/nova-feature`)
5. Abra um Pull Request

## 📝 License

Este projeto está sob a licença MIT. Veja [LICENSE](LICENSE) para mais detalhes.

## 🙏 Agradecimentos

- [GitHub Actions](https://github.com/features/actions)
- [CodeQL](https://codeql.github.com/)
- [Trivy](https://trivy.dev/)
- [Gitleaks](https://gitleaks.io/)
- [tfsec](https://aquasecurity.github.io/tfsec/)
- [Checkov](https://www.checkov.io/)
- [Syft](https://github.com/anchore/syft)

## 📞 Suporte

- 📖 [Documentação](docs/)
- 🐛 [Issues](https://github.com/celfons/agents-default-actions-template/issues)
- 💬 [Discussions](https://github.com/celfons/agents-default-actions-template/discussions)

---

**Desenvolvido com ❤️ para a comunidade de DevSecOps**