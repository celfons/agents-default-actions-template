# Agents Configuration Guide

## Overview

Este guia documenta a configuração de agentes (workflows e actions) para padronização de qualidade e segurança em projetos de software.

## Architecture

O template é composto por:

### 1. **Reusable Workflows** (workflow_call)
- `quality-gate.yml` - Pipeline de qualidade
- `security-scan.yml` - Pipeline de segurança

### 2. **Composite Actions**
- `language-detect` - Detecção automática de linguagem
- `security-scan` - Varredura de segurança consolidada

## Quality Gate Workflow

### Purpose
Executar verificações de qualidade de código: lint, formatação, testes com cobertura e build.

### Usage

```yaml
jobs:
  quality:
    uses: ./.github/workflows/quality-gate.yml
    permissions:
      contents: read
      pull-requests: write
    with:
      language: auto  # auto, nodejs, python, java
      skip-lint: false
      skip-format: false
      skip-test: false
      skip-build: false
      coverage-threshold: 80
      working-directory: '.'
      timeout-minutes: 30
```

### Inputs

| Input | Description | Default | Required |
|-------|-------------|---------|----------|
| `language` | Linguagem do projeto (auto, nodejs, python, java) | `auto` | No |
| `skip-lint` | Pular verificação de lint | `false` | No |
| `skip-format` | Pular verificação de formatação | `false` | No |
| `skip-test` | Pular testes | `false` | No |
| `skip-build` | Pular build | `false` | No |
| `coverage-threshold` | Limite mínimo de cobertura (%) | `80` | No |
| `working-directory` | Diretório de trabalho | `.` | No |
| `timeout-minutes` | Timeout do job em minutos | `30` | No |

### Jobs Executed

1. **detect-language**: Detecta automaticamente a linguagem do projeto
2. **lint**: Executa linters específicos da linguagem
3. **format-check**: Verifica formatação do código
4. **test**: Executa testes com cobertura de código
5. **build**: Compila/empacota o projeto

### Language Detection

O workflow detecta automaticamente a linguagem baseado em arquivos:

#### Node.js
- `package.json`
- `package-lock.json`
- `yarn.lock`
- `pnpm-lock.yaml`

#### Python
- `requirements.txt`
- `setup.py`
- `pyproject.toml`
- `Pipfile`
- `poetry.lock`

#### Java
- `pom.xml`
- `build.gradle`
- `build.gradle.kts`
- `gradlew`

### Tools Used by Language

#### Node.js
- **Lint**: ESLint
- **Format**: Prettier
- **Test**: Jest/npm test (with coverage)
- **Build**: npm build

#### Python
- **Lint**: flake8, pylint
- **Format**: black, isort
- **Test**: pytest (with coverage)
- **Build**: python build

#### Java
- **Lint**: Checkstyle / Gradle check
- **Format**: Spotless
- **Test**: JUnit (with JaCoCo coverage)
- **Build**: Maven / Gradle

## Security Scan Workflow

### Purpose
Executar varreduras de segurança: SAST, SCA, secrets, IaC, containers, SBOM e licenças.

### Usage

```yaml
jobs:
  security:
    uses: ./.github/workflows/security-scan.yml
    permissions:
      contents: read
      security-events: write
      pull-requests: write
    with:
      language: auto
      skip-codeql: false
      skip-dependency-review: false
      skip-secrets: false
      skip-iac: false
      skip-container: false
      skip-sbom: false
      skip-license: false
      dockerfile-path: './Dockerfile'
      working-directory: '.'
      timeout-minutes: 45
    secrets: inherit
```

### Inputs

| Input | Description | Default | Required |
|-------|-------------|---------|----------|
| `language` | Linguagem para CodeQL | `auto` | No |
| `skip-codeql` | Pular análise SAST com CodeQL | `false` | No |
| `skip-dependency-review` | Pular revisão de dependências | `false` | No |
| `skip-secrets` | Pular varredura de secrets | `false` | No |
| `skip-iac` | Pular varredura de IaC | `false` | No |
| `skip-container` | Pular varredura de containers | `false` | No |
| `skip-sbom` | Pular geração de SBOM | `false` | No |
| `skip-license` | Pular verificação de licenças | `false` | No |
| `dockerfile-path` | Caminho para Dockerfile | `./Dockerfile` | No |
| `working-directory` | Diretório de trabalho | `.` | No |
| `timeout-minutes` | Timeout do job em minutos | `45` | No |

### Jobs Executed

1. **codeql-analysis**: SAST com CodeQL (GitHub)
2. **dependency-review**: SCA - análise de dependências vulneráveis
3. **secret-scanning**: Detecção de secrets com Gitleaks
4. **iac-scanning**: Segurança de IaC com tfsec e Checkov
5. **container-scanning**: Segurança de containers com Trivy
6. **sbom-generation**: Geração de SBOM com Syft
7. **license-check**: Verificação de conformidade de licenças

### Security Tools

#### SAST - Static Application Security Testing
- **CodeQL**: Análise semântica de código para vulnerabilidades
- Suporta: JavaScript, Python, Java, C/C++, C#, Go, Ruby

#### SCA - Software Composition Analysis
- **Dependency Review**: Identifica dependências vulneráveis
- Falha em severidade: moderate ou superior
- Licenças permitidas: MIT, Apache-2.0, BSD-2-Clause, BSD-3-Clause, ISC, 0BSD
- Licenças negadas: GPL-3.0, AGPL-3.0

#### Secret Scanning
- **Gitleaks**: Detecta credenciais, tokens e secrets expostos
- Varre todo o histórico do git
- Gera relatório SARIF

#### IaC - Infrastructure as Code
- **tfsec**: Segurança para Terraform
- **Checkov**: Segurança multi-cloud (Terraform, CloudFormation, Kubernetes, etc)
- Frameworks: all
- Gera SARIF para ambos

#### Container Security
- **Trivy**: Scanner de vulnerabilidades em containers
- Severidades: CRITICAL, HIGH, MEDIUM
- Gera SARIF

#### SBOM - Software Bill of Materials
- **Syft**: Geração de SBOM
- Formatos: CycloneDX JSON, SPDX JSON
- Retenção: 30 dias

#### License Compliance
- **license-checker** (Node.js): Análise de licenças npm
- **pip-licenses** (Python): Análise de licenças Python
- Gera relatórios JSON

## Composite Actions

### Language Detect Action

Ação reutilizável para detecção de linguagem.

```yaml
- name: Detect Language
  uses: ./.github/actions/language-detect
  with:
    override-language: auto  # ou nodejs, python, java
```

#### Outputs
- `detected-language`: Linguagem principal detectada
- `has-nodejs`: Se tem arquivos Node.js
- `has-python`: Se tem arquivos Python
- `has-java`: Se tem arquivos Java

### Security Scan Action

Ação placeholder para varredura de segurança consolidada.

```yaml
- name: Security Scan
  uses: ./.github/actions/security-scan
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    language: auto
    working-directory: '.'
```

## Security Best Practices Implemented

### 1. **Pinned Actions**
Todas as actions são pinadas com SHA256 para prevenir supply chain attacks:
```yaml
uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11 # v4.1.1
```

### 2. **Minimum Privileges**
Cada job tem apenas as permissões necessárias:
```yaml
permissions:
  contents: read
  security-events: write
```

### 3. **Timeouts**
Todos os jobs têm timeout configurado:
```yaml
timeout-minutes: 30
```

### 4. **Concurrency**
Previne execuções concorrentes desnecessárias:
```yaml
concurrency:
  group: quality-${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true
```

### 5. **SARIF Upload**
Resultados de segurança em formato SARIF para GitHub Security tab:
```yaml
uses: github/codeql-action/upload-sarif@...
```

### 6. **Artifacts**
Artefatos de build, coverage e SBOM armazenados:
```yaml
uses: actions/upload-artifact@...
with:
  retention-days: 7  # ou 30 para SBOM
```

### 7. **Persist Credentials: false**
Desabilita persistência de credenciais Git:
```yaml
with:
  persist-credentials: false
```

## Integration Examples

### Example 1: Basic CI/CD

```yaml
name: CI/CD
on: [push, pull_request]

jobs:
  quality:
    uses: ./.github/workflows/quality-gate.yml
    with:
      language: auto
  
  security:
    uses: ./.github/workflows/security-scan.yml
    secrets: inherit
```

### Example 2: Node.js Project

```yaml
name: Node.js CI
on: [push, pull_request]

jobs:
  quality:
    uses: ./.github/workflows/quality-gate.yml
    with:
      language: nodejs
      coverage-threshold: 90
  
  security:
    uses: ./.github/workflows/security-scan.yml
    with:
      language: javascript
      skip-container: true  # No Dockerfile
```

### Example 3: Python Project

```yaml
name: Python CI
on: [push, pull_request]

jobs:
  quality:
    uses: ./.github/workflows/quality-gate.yml
    with:
      language: python
      coverage-threshold: 85
  
  security:
    uses: ./.github/workflows/security-scan.yml
    with:
      language: python
```

### Example 4: Java Project

```yaml
name: Java CI
on: [push, pull_request]

jobs:
  quality:
    uses: ./.github/workflows/quality-gate.yml
    with:
      language: java
      timeout-minutes: 45
  
  security:
    uses: ./.github/workflows/security-scan.yml
    with:
      language: java
      dockerfile-path: './src/main/docker/Dockerfile'
```

### Example 5: Monorepo with Multiple Languages

```yaml
name: Monorepo CI
on: [push, pull_request]

jobs:
  frontend-quality:
    uses: ./.github/workflows/quality-gate.yml
    with:
      language: nodejs
      working-directory: './frontend'
  
  backend-quality:
    uses: ./.github/workflows/quality-gate.yml
    with:
      language: python
      working-directory: './backend'
  
  security:
    uses: ./.github/workflows/security-scan.yml
    secrets: inherit
```

## Troubleshooting

### Language Not Detected
- Verifique se os arquivos de manifesto existem (package.json, requirements.txt, pom.xml)
- Use `language: nodejs|python|java` para forçar uma linguagem específica

### Tests Failing
- Certifique-se de que existe um script `test` ou `test:coverage` no package.json (Node.js)
- Para Python, certifique-se de ter pytest instalado
- Para Java, verifique se JUnit está configurado

### CodeQL Analysis Failing
- Verifique se a linguagem é suportada pelo CodeQL
- Para Java/C++, pode ser necessário customizar o build

### Container Scan Skipped
- Verifique se o Dockerfile existe no caminho especificado
- Use `dockerfile-path` para especificar o caminho correto

### Dependency Review Only on PRs
- dependency-review-action só funciona em pull requests
- Para outros eventos, use ferramentas como Dependabot

## Maintenance

### Updating Pinned Actions
Periodicamente, atualize as actions pinadas:
1. Verifique novas versões no GitHub Marketplace
2. Atualize o SHA256 e a versão comentada
3. Teste antes de fazer merge

### Adding New Languages
Para adicionar suporte a novas linguagens:
1. Adicione detecção em `detect-language` job
2. Adicione steps específicos de setup
3. Adicione comandos de lint/format/test/build
4. Atualize documentação

## Support

Para issues ou sugestões:
- Abra uma issue no repositório
- Inclua logs relevantes
- Especifique a linguagem e versão

## License

Este template é open source. Veja LICENSE para detalhes.
