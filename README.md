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

### Opção 1: Usar Action Publicada no Marketplace

Use a action diretamente do GitHub Marketplace para **detecção de linguagem e orientação**:

> **💡 Nota**: Esta action do marketplace fornece detecção automática de linguagem e orientação sobre qual workflow usar. Para funcionalidade completa (lint, test, build, security scans), use as Opções 2 ou 3 abaixo.

```yaml
name: Language Detection

on: [push, pull_request]

jobs:
  detect:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Detect Project Language
        uses: celfons/agents-default-actions-template@v1
        with:
          mode: 'all'
          github-token: ${{ secrets.GITHUB_TOKEN }}
          language: 'auto'
```

A action exibirá a linguagem detectada e fornecerá links para os workflows completos.

### Opção 2: Usar Workflows Reutilizáveis (Recomendado para CI/CD Completo)

Para controle mais granular, copie os workflows para seu repositório:

```bash
# Copie a estrutura de .github
cp -r .github /path/to/your/repo/

# Ou adicione como submódulo
git submodule add https://github.com/celfons/agents-default-actions-template .github/templates
```

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

### Opção 3: Usar Actions Individuais via Path

Você também pode usar as composite actions individuais diretamente via path:

```yaml
name: Custom Pipeline

on: [push, pull_request]

jobs:
  detect:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      # Detectar linguagem do projeto
      - uses: celfons/agents-default-actions-template/.github/actions/language-detect@v1
        id: detect
      
      - run: echo "Detected language: ${{ steps.detect.outputs.detected-language }}"
  
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      # Scanner de segurança consolidado
      - uses: celfons/agents-default-actions-template/.github/actions/security-scan@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          language: auto
```

## 🎨 Exemplos de Uso

### Node.js Project

Usando a action do marketplace:
```yaml
- uses: celfons/agents-default-actions-template@v1
  with:
    mode: quality
    language: nodejs
    coverage-threshold: 90
```

Ou usando workflow reutilizável:
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

### Python Project

Usando a action do marketplace:
```yaml
- uses: celfons/agents-default-actions-template@v1
  with:
    mode: quality
    language: python
    coverage-threshold: 85
    working-directory: './src'
```

Ou usando workflow reutilizável:
```yaml
jobs:
  quality:
    uses: ./.github/workflows/quality-gate.yml
    with:
      language: python
      coverage-threshold: 85
      working-directory: './src'
```

### Java Project

Usando a action do marketplace:
```yaml
- uses: celfons/agents-default-actions-template@v1
  with:
    mode: quality
    language: java
```

Ou usando workflow reutilizável:
```yaml
jobs:
  quality:
    uses: ./.github/workflows/quality-gate.yml
    with:
      language: java
      timeout-minutes: 45
```

### Monorepo

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

## 📋 Recursos Disponíveis

### 🎯 Action Principal (Marketplace)

A action principal (`action.yml`) está na raiz do repositório e pode ser publicada no GitHub Marketplace. Ela serve como ponto de entrada consolidado para todas as funcionalidades.

**Uso via Marketplace:**
```yaml
- uses: celfons/agents-default-actions-template@v1
  with:
    mode: 'all'  # quality, security, ou all
    github-token: ${{ secrets.GITHUB_TOKEN }}
    language: 'auto'
```

**Inputs Principais**:
| Input | Descrição | Default |
|-------|-----------|---------|
| `mode` | Modo de scanning: quality, security, ou all | `all` |
| `github-token` | Token do GitHub | obrigatório |
| `language` | Linguagem (auto, nodejs, python, java) | `auto` |
| `working-directory` | Diretório de trabalho | `.` |
| `coverage-threshold` | Cobertura mínima (%) | `80` |
| `skip-lint` | Pular lint | `false` |
| `skip-format` | Pular format check | `false` |
| `skip-test` | Pular testes | `false` |
| `skip-build` | Pular build | `false` |
| `skip-codeql` | Pular CodeQL | `false` |
| `skip-secrets` | Pular secret scanning | `false` |
| `skip-iac` | Pular IaC scanning | `false` |
| `dockerfile-path` | Caminho do Dockerfile | `./Dockerfile` |

**Outputs**:
| Output | Descrição |
|--------|-----------|
| `detected-language` | Linguagem detectada |
| `quality-passed` | Se checks de qualidade passaram |
| `security-completed` | Se scans de segurança completaram |

### 🔄 Workflows Reutilizáveis

#### Quality Gate (`quality-gate.yml`)

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

#### Security Scan (`security-scan.yml`)

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

## 🧩 Composite Actions (Uso via Path)

As actions em subpastas podem ser usadas diretamente via path reference:

### Language Detect

**Path**: `.github/actions/language-detect`

Detecta automaticamente a linguagem do projeto.

**Uso Local (após clonar o repositório):**
```yaml
- uses: ./.github/actions/language-detect
  id: detect
  with:
    override-language: auto

- run: echo "Language: ${{ steps.detect.outputs.detected-language }}"
```

**Uso Remoto (via path do GitHub):**
```yaml
- uses: celfons/agents-default-actions-template/.github/actions/language-detect@v1
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

### Security Scan

**Path**: `.github/actions/security-scan`

Action consolidada para varredura de segurança.

**Uso Local (após clonar o repositório):**
```yaml
- uses: ./.github/actions/security-scan
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    language: auto
```

**Uso Remoto (via path do GitHub):**
```yaml
- uses: celfons/agents-default-actions-template/.github/actions/security-scan@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    language: auto
    working-directory: '.'
```

**Inputs**:
| Input | Descrição | Default |
|-------|-----------|---------|
| `github-token` | Token do GitHub | obrigatório |
| `language` | Linguagem para análise | `auto` |
| `working-directory` | Diretório de trabalho | `.` |
| `dockerfile-path` | Caminho do Dockerfile | `./Dockerfile` |
| `skip-codeql` | Pular CodeQL | `false` |
| `skip-secrets` | Pular secret scanning | `false` |
| `skip-iac` | Pular IaC scanning | `false` |

**Outputs**:
| Output | Descrição |
|--------|-----------|
| `scan-completed` | Se scans completaram |
| `vulnerabilities-found` | Se vulnerabilidades foram encontradas |

## 📚 Documentação

- **[agents.md](docs/agents.md)**: Guia completo de configuração dos workflows
- **[skills.md](docs/skills.md)**: Referência de skills e capacidades
- **[example-usage.yml](.github/workflows/example-usage.yml)**: Exemplo de uso

## 📦 Publicação no GitHub Marketplace

Este repositório está estruturado para permitir publicação no GitHub Actions Marketplace:

### ✅ Estrutura para Marketplace

1. **Action Principal (Root)**: O arquivo `action.yml` na raiz permite publicação no marketplace
2. **Actions em Subpastas**: As actions em `.github/actions/*` podem ser usadas via path reference
3. **Workflows Reutilizáveis**: Os workflows em `.github/workflows/*` podem ser usados via `workflow_call`

### 🚀 Como Publicar no Marketplace

Para publicar esta action no GitHub Marketplace:

1. **Criar Release com Tag**: Crie uma release no GitHub com uma tag (ex: `v1.0.0`)
   ```bash
   git tag -a v1.0.0 -m "Release v1.0.0"
   git push origin v1.0.0
   ```

2. **Publicar no Marketplace**: No GitHub, vá em Releases → escolha a release → marque "Publish this Action to the GitHub Marketplace"

3. **Manter Tags Móveis**: É uma boa prática manter tags móveis como `v1` apontando para a última versão estável:
   ```bash
   git tag -fa v1 -m "Update v1 to v1.0.0"
   git push origin v1 --force
   ```

### 📝 Recomendações

- ✅ **Action Root**: Serve como ponto de entrada único para o marketplace
- ✅ **Path Reference**: Actions em subpastas acessíveis via `owner/repo/.github/actions/name@ref`
- ✅ **Workflows**: Workflows reutilizáveis acessíveis via `owner/repo/.github/workflows/name.yml@ref`
- ✅ **Versioning**: Use semantic versioning (v1.0.0, v1.1.0, etc.)
- ✅ **Branding**: Configurado com ícone e cor no `action.yml`

### 🔗 Formas de Uso

| Método | Caminho | Publicável no Marketplace |
|--------|---------|---------------------------|
| Action Root | `owner/repo@v1` | ✅ Sim |
| Action Subpasta | `owner/repo/.github/actions/name@v1` | ❌ Não (mas usável via path) |
| Workflow Reutilizável | `owner/repo/.github/workflows/name.yml@v1` | ❌ Não (mas usável via workflow_call) |

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