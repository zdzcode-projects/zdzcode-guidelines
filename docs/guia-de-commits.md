# 📝 Guia de Commits e Branches

Este documento define as convenções detalhadas de commits e branches utilizadas nos projetos ZDZCode, garantindo histórico limpo e colaboração eficiente.

## 📋 Índice

- [Mensagens de Commit](#mensagens-de-commit)
- [Estratégia de Branches](#estratégia-de-branches)
- [Tags e Releases](#tags-e-releases)
- [Exemplos Práticos](#exemplos-práticos)
- [Ferramentas Recomendadas](#ferramentas-recomendadas)

---

## Mensagens de Commit

### Formato Padrão

```
<tipo>(<escopo>): <descrição>

[corpo opcional]

[rodapé opcional]
```

### Tipos de Commit

| Tipo | Descrição | Exemplo |
|------|-----------|---------|
| `feat` | Nova funcionalidade | `feat: adicionar autenticação JWT` |
| `fix` | Correção de bug | `fix: corrigir cálculo de desconto` |
| `docs` | Documentação | `docs: atualizar README de instalação` |
| `style` | Formatação, lint | `style: aplicar formatação ESLint` |
| `refactor` | Refatoração sem mudança de funcionalidade | `refactor: extrair lógica de validação` |
| `test` | Testes | `test: adicionar testes para UserService` |
| `chore` | Manutenção, build, dependências | `chore: atualizar dependências npm` |
| `perf` | Melhoria de performance | `perf: otimizar consulta de produtos` |
| `ci` | Integração contínua | `ci: adicionar cache no pipeline` |
| `build` | Sistema de build | `build: configurar webpack production` |

### Escopo (Opcional)

Indica a área afetada pela alteração:

```bash
feat(auth): implementar login com Google
fix(payment): corrigir validação de cartão
docs(api): atualizar documentação de endpoints
```

### Regras de Escrita

#### ✅ Boas Práticas
- **Imperativo**: "adiciona", "corrige", "atualiza"
- **Português**: Idioma consistente em todo projeto
- **Conciso**: Máximo 50 caracteres no título
- **Específico**: Evite termos genéricos

#### ❌ Evitar
```bash
# Muito genérico
git commit -m "fix: correções"
git commit -m "chore: updates"

# Não imperativo  
git commit -m "feat: adicionando login"
git commit -m "fix: corrigiu bug"
```

#### ✅ Exemplos Corretos
```bash
feat: implementar sistema de notificações por email
fix: corrigir vazamento de memória no cache Redis  
docs: adicionar exemplos de uso da API REST
refactor: simplificar lógica de cálculo de impostos
test: cobrir cenários de falha na autenticação
```

### Corpo da Mensagem

Para commits complexos, use o corpo para explicar:
- **O que** mudou
- **Por que** mudou  
- **Como** pode afetar o comportamento

```bash
feat: implementar cache distribuído com Redis

- Adiciona suporte a cache em cluster
- Melhora performance em 40% para consultas frequentes
- Mantém compatibilidade com cache em memória local

Resolves #142
```

### Rodapé

#### Referências a Issues
```bash
Fixes #123
Closes #456  
Resolves #789
See #101
```

#### Breaking Changes
```bash
BREAKING CHANGE: endpoint /api/users agora requer autenticação
```

---

## Estratégia de Branches

### Git Flow Simplificado

#### Branch Principal
- **`main`** - Código estável e pronto para produção
- **Protegida** - Merges apenas via Pull Request
- **Deploy automático** - Integração com pipelines de produção

#### Branches de Desenvolvimento
```
main
├── feature/implementar-pagamento-pix     # Nova funcionalidade
├── fix/corrigir-calculo-juros           # Correção de bug
├── hotfix/corrigir-falha-critica        # Correção urgente
└── release/v2.1.0                       # Preparação de release
```

### Convenções de Nomenclatura

#### Formato Padrão
```
<tipo>/<descrição-kebab-case>
```

#### Tipos de Branch

| Tipo | Propósito | Exemplo | Merge para |
|------|-----------|---------|------------|
| `feature/` | Novas funcionalidades | `feature/dashboard-analytics` | `main` |
| `fix/` | Correções não urgentes | `fix/validacao-email-duplicado` | `main` |
| `hotfix/` | Correções urgentes | `hotfix/memoria-vazamento-critico` | `main` |
| `docs/` | Documentação | `docs/api-reference-update` | `main` |
| `chore/` | Manutenção | `chore/update-dependencies` | `main` |
| `release/` | Preparação de release | `release/v1.2.0` | `main` |

#### Regras de Nomenclatura
- **kebab-case**: Separar palavras com hífen
- **Descritivo**: Descrever claramente o propósito
- **Conciso**: Evitar nomes excessivamente longos
- **Sem prefixos pessoais**: Evitar `joao/feature-x`

### Ciclo de Vida das Branches

1. **Criação**
   ```bash
   git checkout main
   git pull origin main
   git checkout -b feature/nova-funcionalidade
   ```

2. **Desenvolvimento**
   ```bash
   # Commits frequentes
   git add .
   git commit -m "feat: implementar base da funcionalidade"
   ```

3. **Sincronização**
   ```bash
   # Manter branch atualizada
   git fetch origin
   git rebase origin/main
   ```

4. **Pull Request**
   - Criar PR quando feature estiver completa
   - Solicitar revisão de código
   - Executar testes automatizados

5. **Merge e Limpeza**
   ```bash
   # Após merge aprovado
   git checkout main
   git pull origin main
   git branch -d feature/nova-funcionalidade
   ```

### Estratégias de Merge

#### Squash and Merge (Recomendado)
- **Quando usar**: Features pequenas a médias
- **Vantagem**: Histórico linear e limpo
- **Resultado**: Um commit por feature no main

#### Merge Commit
- **Quando usar**: Features grandes com commits importantes
- **Vantagem**: Preserva histórico detalhado
- **Resultado**: Commit de merge visível

#### Rebase and Merge
- **Quando usar**: Contribuições individuais pequenas
- **Vantagem**: Histórico completamente linear
- **Resultado**: Como se tivesse sido feito direto no main

---

## Tags e Releases

### Semantic Versioning

Seguimos o padrão [Semantic Versioning](https://semver.org/lang/pt-BR/):

```
MAJOR.MINOR.PATCH
```

#### Incremento de Versão

| Tipo | Quando Incrementar | Exemplo |
|------|-------------------|---------|
| **MAJOR** | Breaking changes | `1.2.3` → `2.0.0` |
| **MINOR** | Novas funcionalidades compatíveis | `1.2.3` → `1.3.0` |
| **PATCH** | Correções de bugs | `1.2.3` → `1.2.4` |

#### Versões Pre-release
```bash
1.2.0-alpha.1    # Versão alfa
1.2.0-beta.2     # Versão beta  
1.2.0-rc.1       # Release candidate
```

### Criação de Tags

```bash
# Tag anotada (recomendado)
git tag -a v1.2.0 -m "Release 1.2.0: Adiciona sistema de pagamentos"

# Tag simples
git tag v1.2.0

# Push das tags
git push origin --tags
```

### Processo de Release

1. **Preparação**
   ```bash
   git checkout main
   git pull origin main
   git checkout -b release/v1.2.0
   ```

2. **Finalização**
   - Atualizar `CHANGELOG.md`
   - Incrementar versão em `package.json` ou `.csproj`
   - Testes finais

3. **Merge e Tag**
   ```bash
   git checkout main
   git merge --no-ff release/v1.2.0
   git tag -a v1.2.0 -m "Release 1.2.0"
   git push origin main --tags
   ```

4. **Limpeza**
   ```bash
   git branch -d release/v1.2.0
   ```

---

## Exemplos Práticos

### Cenário 1: Nova Funcionalidade
```bash
# 1. Criar branch
git checkout -b feature/sistema-notificacoes

# 2. Desenvolvimento com commits incrementais
git commit -m "feat(notification): criar estrutura base de notificações"
git commit -m "feat(notification): implementar envio por email"  
git commit -m "feat(notification): adicionar templates personalizáveis"
git commit -m "test(notification): cobrir cenários de falha"

# 3. PR e merge squash resultará em:
# "feat: implementar sistema de notificações (#123)"
```

### Cenário 2: Correção de Bug
```bash
git checkout -b fix/calculo-desconto-produto

git commit -m "fix: corrigir cálculo de desconto para produtos em promoção"
git commit -m "test: adicionar casos de teste para descontos"

# Merge: "fix: corrigir cálculo de desconto para produtos em promoção (#124)"
```

### Cenário 3: Hotfix Crítico
```bash
git checkout -b hotfix/vazamento-memoria-cache

git commit -m "hotfix: corrigir vazamento de memória no cache Redis"
git commit -m "test: verificar limpeza adequada de recursos"

# Deploy imediato após merge
```

---

## Ferramentas Recomendadas

### Commitizen
Padroniza criação de commits interativamente:

```bash
# Instalar globalmente
npm install -g commitizen cz-conventional-changelog

# Usar
git cz
```

### Conventional Changelog
Gera changelog automaticamente:

```bash
npm install -g conventional-changelog-cli

# Gerar changelog
conventional-changelog -p angular -i CHANGELOG.md -s
```

### Husky + Lint-staged
Validação automática antes de commits:

```json
{
  "husky": {
    "hooks": {
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS",
      "pre-commit": "lint-staged"
    }
  }
}
```

### Git Hooks
Scripts de validação no repositório:

```bash
#!/bin/sh
# .git/hooks/commit-msg
# Validar formato da mensagem de commit

commit_regex='^(feat|fix|docs|style|refactor|test|chore)(\(.+\))?: .{1,50}'

if ! grep -qE "$commit_regex" "$1"; then
    echo "Formato de commit inválido!"
    echo "Use: tipo(escopo): descrição"
    exit 1
fi
```

---

## 📚 Referências

- **[Conventional Commits](https://www.conventionalcommits.org/pt-br/)** - Especificação padrão
- **[Semantic Versioning](https://semver.org/lang/pt-BR/)** - Versionamento semântico
- **[Git Flow](https://nvie.com/posts/a-successful-git-branching-model/)** - Modelo de branching
- **[Padrões e Diretrizes](./padroes-e-diretrizes.md)** - Documento base do ZDZCode

---

*Última atualização: Dezembro 2024 • Versão 2.0*
