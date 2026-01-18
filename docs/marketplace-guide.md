# Guia Rápido: Publicação no GitHub Marketplace

## 📦 Estrutura do Repositório para Marketplace

Este repositório está configurado para suportar **publicação no GitHub Marketplace** mantendo a flexibilidade de uso via paths.

### ✅ Arquitetura

```
agents-default-actions-template/
├── action.yml                          # ✅ Publicável no Marketplace
├── .github/
│   ├── actions/
│   │   ├── language-detect/
│   │   │   └── action.yml             # ❌ Não publicável, mas usável via path
│   │   └── security-scan/
│   │       └── action.yml             # ❌ Não publicável, mas usável via path
│   └── workflows/
│       ├── quality-gate.yml           # ❌ Não publicável, mas usável via workflow_call
│       ├── security-scan.yml          # ❌ Não publicável, mas usável via workflow_call
│       └── all-usage-patterns.yml     # Exemplos de uso
└── README.md
```

## 🎯 Formas de Uso

### 1️⃣ Action Root (Marketplace) - Para detecção de linguagem e descoberta

```yaml
- uses: celfons/agents-default-actions-template@v1
  with:
    mode: all
    github-token: ${{ secrets.GITHUB_TOKEN }}
```

**O que faz:**
- ✅ Detecta automaticamente a linguagem do projeto
- ✅ Fornece orientação sobre quais workflows usar
- ✅ Exibe resumo no GitHub Actions Summary
- ✅ Publicável no GitHub Marketplace para fácil descoberta

**Limitações:**
- ❌ Não executa lint, test, build ou security scans completos
- ❌ Serve como ponto de entrada e orientação

**Quando usar:**
- Para descobrir rapidamente a linguagem do projeto
- Como ponto de entrada para novos usuários
- Para obter orientação sobre qual workflow usar
- Para testes e experimentação

### 2️⃣ Workflows Reutilizáveis - RECOMENDADO para CI/CD completo

```yaml
jobs:
  quality:
    uses: celfons/agents-default-actions-template/.github/workflows/quality-gate.yml@v1
    with:
      language: nodejs
```

**Vantagens:**
- ✅ Controle granular de cada etapa
- ✅ Separação entre quality e security
- ✅ Múltiplos jobs paralelos
- ✅ Configuração detalhada

**Quando usar:**
- Para projetos que precisam de controle fino
- Para executar quality e security separadamente
- Para monorepos com múltiplas linguagens

### 3️⃣ Actions via Path - RECOMENDADO para composição customizada

```yaml
- uses: celfons/agents-default-actions-template/.github/actions/language-detect@v1
  id: detect
- uses: celfons/agents-default-actions-template/.github/actions/security-scan@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
```

**Vantagens:**
- ✅ Máxima flexibilidade
- ✅ Composição de workflows personalizados
- ✅ Reutilização de componentes individuais
- ✅ Integração com outras actions

**Quando usar:**
- Para criar workflows completamente customizados
- Para integrar com outras ferramentas
- Para usar apenas detecção de linguagem ou security scan

## 🚀 Como Publicar no Marketplace

### Passo 1: Verificar action.yml

Certifique-se de que o arquivo `action.yml` na raiz está correto:

```yaml
name: 'Quality & Security Scanner'
description: 'Comprehensive quality and security scanning...'
author: 'celfons'
branding:
  icon: 'shield'
  color: 'blue'
```

### Passo 2: Criar Release

```bash
# Criar tag
git tag -a v1.0.0 -m "First marketplace release"
git push origin v1.0.0

# Criar tag móvel (recomendado)
git tag -fa v1 -m "Latest v1.x.x"
git push origin v1 --force
```

### Passo 3: Publicar no Marketplace

1. Vá para: https://github.com/celfons/agents-default-actions-template/releases
2. Clique em "Draft a new release"
3. Selecione a tag `v1.0.0`
4. Marque "✅ Publish this Action to the GitHub Marketplace"
5. Preencha a descrição e categoria
6. Clique em "Publish release"

### Passo 4: Verificar

Acesse: https://github.com/marketplace/actions/quality-security-scanner

## 📋 Checklist de Publicação

- [ ] `action.yml` está na raiz do repositório
- [ ] `name`, `description` e `author` estão preenchidos
- [ ] `branding` (icon e color) está configurado
- [ ] Todos os inputs e outputs estão documentados
- [ ] README.md tem exemplos de uso
- [ ] CHANGELOG.md está atualizado
- [ ] Tag v1.0.0 foi criada
- [ ] Release foi publicada no GitHub
- [ ] "Publish to Marketplace" foi marcado
- [ ] Action aparece no marketplace

## 🔄 Manutenção de Versões

### Semantic Versioning

Use versionamento semântico:
- `v1.0.0` - Release inicial
- `v1.1.0` - Novas features (backwards compatible)
- `v1.0.1` - Bug fixes
- `v2.0.0` - Breaking changes

### Tags Móveis

Mantenha tags móveis para facilitar o uso:

```bash
# Quando lançar v1.2.3
git tag -a v1.2.3 -m "Release v1.2.3"
git push origin v1.2.3

# Atualize v1 para apontar para v1.2.3
git tag -fa v1 -m "Update v1 to v1.2.3"
git push origin v1 --force
```

Usuários podem usar:
- `@v1` - Sempre a última versão v1.x.x (recomendado)
- `@v1.2.3` - Versão específica (para produção crítica)
- `@main` - Última versão (não recomendado para produção)

## 🎨 Branding

O arquivo `action.yml` inclui branding para o marketplace:

```yaml
branding:
  icon: 'shield'    # Ícone de segurança
  color: 'blue'     # Cor azul
```

Ícones disponíveis: https://feathericons.com/
Cores disponíveis: white, yellow, blue, green, orange, red, purple, gray-dark

## 📊 Comparação de Métodos

| Método | Publicável Marketplace | Uso Local | Uso Remoto | Flexibilidade |
|--------|------------------------|-----------|------------|---------------|
| Action Root | ✅ Sim | ✅ Sim | ✅ Sim | ⭐⭐⭐ |
| Workflow Reutilizável | ❌ Não | ✅ Sim | ✅ Sim | ⭐⭐⭐⭐⭐ |
| Action via Path | ❌ Não | ✅ Sim | ✅ Sim | ⭐⭐⭐⭐ |

## 🔗 Links Úteis

- [GitHub Actions Marketplace](https://github.com/marketplace?type=actions)
- [Publishing Actions](https://docs.github.com/en/actions/creating-actions/publishing-actions-in-github-marketplace)
- [Action Metadata](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions)
- [Reusable Workflows](https://docs.github.com/en/actions/using-workflows/reusing-workflows)

## 💡 Dicas

1. **Use tags móveis**: Facilita a atualização para usuários
2. **Documente tudo**: README e CHANGELOG completos
3. **Teste antes de publicar**: Use o workflow all-usage-patterns.yml
4. **Mantenha compatibilidade**: Evite breaking changes em minor versions
5. **Responda issues**: Marketplace valoriza projetos ativos

## ❓ FAQ

**P: Por que não posso publicar as actions em subpastas?**
R: É uma limitação do GitHub Marketplace. Apenas o `action.yml` na raiz pode ser publicado.

**P: Posso ainda usar as actions em subpastas?**
R: Sim! Use via path reference: `owner/repo/.github/actions/name@ref`

**P: Qual método devo usar?**
R: Depende do caso:
- Simplicidade → Action Root (marketplace)
- Controle granular → Workflows Reutilizáveis
- Composição customizada → Actions via Path

**P: Como atualizar a action no marketplace?**
R: Crie uma nova tag e release. Atualize a tag móvel (v1) para apontar para a nova versão.

**P: Posso ter múltiplas actions no marketplace?**
R: Tecnicamente sim, mas apenas a action na raiz é publicável diretamente. Actions em subpastas precisam ser usadas via path reference (`owner/repo/.github/actions/name@v1`), mas não aparecem como entradas separadas no marketplace.

**P: Como testar antes de publicar?**
R: Use o workflow `.github/workflows/all-usage-patterns.yml` para testar. Antes de criar a tag v1, os exemplos que referenciam `@v1` devem usar caminhos relativos ou `@main`.
