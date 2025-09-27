# Padrões e Diretrizes ZDZCode

Este documento estabelece as boas práticas e padrões fundamentais para todos os projetos mantidos pela comunidade ZDZCode. Seguir estas diretrizes garante consistência, qualidade e facilita a colaboração entre equipes.

## 📋 Índice

- [Estrutura de Repositórios](#estrutura-de-repositórios)
- [Convenções de Branches](#branches)
- [Estilo de Código](#estilo-de-código)
- [Commits](#commits)
- [Pull Requests](#pull-requests)
- [Revisão de Código](#revisão-de-código)
- [Issues](#issues)
- [Documentação](#documentação)
- [Integração Contínua](#integração-contínua)
- [Licença](#licença)

---

## Estrutura de Repositórios

### Requisitos Básicos

1. **README.md Obrigatório**
   - Descrição clara do objetivo do projeto
   - Instruções de instalação e configuração
   - Exemplos de uso básicos
   - Informações sobre contribuição

2. **Organização de Arquivos**
   - Mantenha arquivos de configuração na raiz ou em pastas dedicadas (`config/`, `.github/`)
   - Scripts de automação em pasta `scripts/` ou `tools/`
   - Documentação adicional em pasta `docs/`

3. **Convenções de Nomenclatura**
   - Nomes de repositório em kebab-case: `meu-projeto-web`
   - Pastas em lowercase ou camelCase conforme convenção da linguagem
   - Arquivos seguindo padrões da stack tecnológica

### Estruturas Recomendadas

#### Projetos .NET (Clean Architecture)
```
src/
├── Domain/           # Entidades e regras de negócio
├── Application/      # Casos de uso e interfaces
├── Infrastructure/   # Implementações técnicas
├── API/             # Controllers e configurações
└── Tests/           # Testes organizados por camada
```

#### Projetos Frontend (Nuxt 3)
```
assets/              # Recursos estáticos
components/          # Componentes Vue reutilizáveis
layouts/            # Layouts da aplicação
pages/              # Páginas com roteamento automático
plugins/            # Plugins e configurações
stores/             # Estados globais (Pinia)
```

## Branches

### Estrutura de Branches

- **Branch Principal**: Sempre `main` (nunca `master`)
- **Branches de Desenvolvimento**: Use prefixos padronizados seguidos de descrição curta

### Convenções de Nomenclatura

Formato: `<tipo>/<descricao-curta-em-kebab-case>`

#### Tipos de Branch
- `feature/` - Novas funcionalidades
- `fix/` ou `bugfix/` - Correções de bugs
- `docs/` - Alterações na documentação
- `chore/` - Manutenção, refatoração, atualizações de dependências
- `hotfix/` - Correções urgentes em produção
- `release/` - Preparação de releases

#### Exemplos Válidos
```bash
feature/implementar-autenticacao-jwt
fix/corrigir-calculo-desconto
docs/atualizar-readme-instalacao
chore/atualizar-dependencias-npm
hotfix/corrigir-vazamento-memoria
```

#### Práticas Recomendadas
- **Evite nomes genéricos**: `feature/nova-funcionalidade` ❌
- **Seja específico**: `feature/adicionar-filtro-por-data` ✅
- **Limite a 50 caracteres** quando possível
- **Use kebab-case** para separar palavras

### Gestão de Branches
- Mantenha branches focadas em uma única responsabilidade
- Delete branches após merge bem-sucedido
- Mantenha histórico linear sempre que possível
## Estilo de Código

### Princípios Fundamentais

1. **Código Limpo**
   - Prefira código autoexplicativo a comentários excessivos
   - Use nomes descritivos para variáveis, métodos e classes
   - Mantenha funções pequenas e focadas em uma responsabilidade

2. **Consistência**
   - Siga as convenções da linguagem/framework
   - Use ferramentas de formatação automática
   - Mantenha padrões consistentes em todo o projeto

### Ferramentas por Stack

#### .NET/C#
- **EditorConfig**: Configurações de formato do editor
- **StyleCop**: Análise estática de estilo
- **SonarQube**: Qualidade e segurança do código

#### Frontend (Vue/Nuxt)
- **ESLint**: Linting com configuração flat config
- **Prettier**: Formatação automática de código
- **TypeScript**: Tipagem estática para JavaScript

#### Configurações Obrigatórias
```json
// .editorconfig (raiz do projeto)
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = true

[*.{js,ts,vue}]
indent_style = space
indent_size = 2

[*.{cs}]
indent_style = space
indent_size = 4
```
## Commits

### Regras Essenciais

1. **Imperativo**: "Adiciona funcionalidade X" ✅ (não "Adicionando" ou "Adicionado")
2. **Português**: Todas as mensagens em português brasileiro
3. **Limite de Caracteres**: Primeira linha máximo 72 caracteres
4. **Atomicidade**: Um commit = uma alteração lógica

### Formato da Mensagem

```
<tipo>: <descrição concisa>

[corpo opcional explicando o "porquê"]

[rodapé opcional com referências]
```

#### Tipos de Commit
- `feat`: Nova funcionalidade
- `fix`: Correção de bug
- `docs`: Alterações na documentação
- `style`: Formatação, ponto e vírgula ausente, etc
- `refactor`: Refatoração sem alterar funcionalidade
- `test`: Adição ou correção de testes
- `chore`: Atualizações de build, dependências, etc

#### Exemplos
```bash
feat: adiciona autenticação com JWT

fix: corrige cálculo de desconto para produtos em promoção

docs: atualiza README com instruções de deploy

chore: atualiza dependências do npm para versões LTS
```

### Agrupamento de Alterações
- Agrupe alterações relacionadas em um único commit
- Evite commits com muitas responsabilidades diferentes
- Use commits separados para alterações independentes

💡 **Dica**: Para detalhes sobre convenções específicas, consulte o [Guia de Commits](./guia-de-commits.md).

## Pull Requests

### Estrutura do PR

#### Título
- Seja conciso e descritivo
- Use o mesmo padrão dos commits quando aplicável
- Evite títulos genéricos como "Correções" ou "Melhorias"

#### Descrição
Inclua sempre:
1. **O que** está sendo alterado
2. **Por que** a alteração é necessária
3. **Como** testar as alterações
4. **Referência** à issue relacionada (se aplicável)

#### Template Sugerido
```markdown
## Descrição
Breve descrição das alterações realizadas.

## Motivação
Por que essa alteração é necessária?

## Como testar
1. Execute `npm install`
2. Execute `npm run dev`
3. Acesse `/nova-funcionalidade`
4. Verifique se...

## Checklist
- [ ] Código testado localmente
- [ ] Testes automatizados passando
- [ ] Documentação atualizada
- [ ] Sem breaking changes (ou marcado como tal)

Fixes #123
```

### Boas Práticas

- **Escopo Focado**: Um PR = uma responsabilidade
- **Tamanho Gerenciável**: Máximo 400 linhas alteradas quando possível
- **Auto-Review**: Revise seu próprio código antes de solicitar review
- **Contexto**: Comente trechos que merecem atenção especial
- **Responsividade**: Responda a comentários em até 24h

## Revisão de Código

### Processo de Review

#### Requisitos Obrigatórios
- **Mínimo 1 reviewer** para cada PR
- **Todos os testes** devem estar passando
- **Discussões resolvidas** antes do merge
- **Approval explícito** do revisor

#### Tipos de Review

1. **Review de Código**
   - Lógica e estrutura
   - Padrões e convenções
   - Performance e segurança
   - Legibilidade e manutenibilidade

2. **Review de Testes**
   - Cobertura adequada
   - Casos de teste relevantes
   - Qualidade dos testes

3. **Review de Documentação**
   - Clareza e completude
   - Exemplos funcionais
   - Atualização de changelog

### Diretrizes para Revisores

#### Comentários Construtivos
- **Seja específico**: Explique o problema e sugira soluções
- **Seja educativo**: Compartilhe conhecimento e melhores práticas
- **Seja respeitoso**: Critique o código, não a pessoa

#### Exemplos de Bons Comentários
```
❌ "Isso está errado"
✅ "Este método pode causar uma exception NullReference. 
    Considere adicionar uma validação: if (user?.IsActive == true)"

❌ "Mude isso"
✅ "Para melhor performance, podemos usar Where().FirstOrDefault() 
    ao invés de First() quando não há garantia de existir o elemento"
```

### Diretrizes para Autores

- **Solicite review** proativamente, não espere
- **Contextualize**: Comente código complexo ou decisões não óbvias
- **Seja receptivo**: Aceite feedback como oportunidade de melhoria
- **Corrija rapidamente**: Implemente sugestões em tempo hábil

## Issues

### Criação de Issues

#### Tipos de Issue
- **🐛 Bug Report**: Problemas ou comportamentos inesperados
- **✨ Feature Request**: Solicitação de nova funcionalidade
- **📚 Documentation**: Melhorias na documentação
- **🔧 Maintenance**: Tarefas de manutenção e refatoração

#### Estrutura Obrigatória

**Para Bugs:**
```markdown
## Descrição do Bug
Descrição clara e concisa do problema.

## Passos para Reproduzir
1. Acesse '...'
2. Clique em '...'
3. Veja o erro

## Comportamento Esperado
O que deveria acontecer.

## Comportamento Atual  
O que está acontecendo.

## Ambiente
- SO: [Windows 10, macOS, Ubuntu]
- Browser: [Chrome 98, Firefox 95]
- Versão: [v1.2.3]
```

**Para Features:**
```markdown
## Funcionalidade Solicitada
Descrição clara da funcionalidade desejada.

## Motivação
Por que essa funcionalidade é importante?

## Comportamento Esperado
Como a funcionalidade deveria funcionar?

## Alternativas Consideradas
Outras abordagens que foram consideradas.
```

### Organização e Gestão

#### Labels Padronizadas
- `bug` - Problemas confirmados
- `enhancement` - Melhorias e novas features  
- `documentation` - Relacionado à documentação
- `good first issue` - Bom para iniciantes
- `help wanted` - Precisa de ajuda da comunidade
- `wontfix` - Não será implementado
- `duplicate` - Issue duplicada

#### Priorização
- `priority: high` - Crítico para funcionamento
- `priority: medium` - Importante mas não urgente
- `priority: low` - Nice to have

#### Associação com PRs
- Sempre associe PRs às issues correspondentes
- Use palavras-chave no PR: `Fixes #123`, `Closes #456`, `Resolves #789`
- Mantenha issues atualizadas com progresso
## Licença

Todos os projetos e documentos do ecossistema ZDZCode seguem a **licença MIT**, que permite uso comercial e modificação, mantendo atribuição aos autores originais.

Para novos projetos, inclua sempre o arquivo `LICENSE` na raiz do repositório com o texto completo da licença MIT.

---

## 📚 Documentos Relacionados

- **[Guia de Commits](./guia-de-commits.md)** - Convenções detalhadas para commits e branches
- **[Guia de Entidades](./guia-entidades.md)** - Padrões específicos para backend .NET
- **[Orientações para Desenvolvedores](./orientacoes-desenvolvedores.md)** - Fluxo de trabalho e exemplos
- **[Guia para Agentes IA](../AGENTS.md)** - Informações técnicas para automação

---

## 🔄 Versionamento deste Documento

| Versão | Data | Alterações |
|--------|------|------------|
| 2.0 | Dez 2024 | Reestruturação completa, novos padrões CI/CD |
| 1.0 | 2024 | Versão inicial |

**Próxima revisão**: Março 2025

## Documentação

### Documentação Obrigatória

#### README.md
Todo projeto deve conter um README completo com:

```markdown
# Nome do Projeto

Breve descrição do que o projeto faz.

## 🚀 Tecnologias

- [Tecnologia 1](link) - Descrição
- [Tecnologia 2](link) - Descrição

## 📋 Pré-requisitos

- Node.js 20.12+
- .NET 8.0+
- SQL Server 2019+

## 🔧 Instalação

1. Clone o repositório
   ```bash
   git clone https://github.com/user/repo.git
   ```

2. Instale dependências
   ```bash
   npm install
   ```

3. Configure variáveis de ambiente
   ```bash
   cp .env.example .env
   ```

## ⚡ Uso

Como executar o projeto localmente:

```bash
npm run dev
```

## 🧪 Testes

```bash
npm run test
```

## 📦 Deploy

Instruções para deploy em produção.

## 🤝 Contribuindo

1. Fork o projeto
2. Crie uma branch para sua feature
3. Commit suas mudanças  
4. Push para a branch
5. Abra um Pull Request

## 📝 Licença

Este projeto está sob a licença MIT - veja [LICENSE](LICENSE) para detalhes.
```

#### Pasta docs/
- **Guias técnicos** detalhados
- **Exemplos de uso** avançados
- **Arquitetura** e decisões técnicas
- **Changelog** com histórico de versões

### Padrões de Escrita

- **Linguagem**: Português brasileiro claro e objetivo
- **Formatação**: Markdown consistente com linting
- **Código**: Exemplos testados e funcionais
- **Links**: Sempre atualizados e válidos
- **Imagens**: Otimizadas e com alt-text descritivo

## Integração Contínua

### Azure DevOps Pipelines

#### Pipeline Básico (.NET)
```yaml
trigger:
- main
- develop

pool:
  vmImage: 'ubuntu-latest'

variables:
  buildConfiguration: 'Release'

steps:
- task: UseDotNet@2
  inputs:
    version: '8.x'
    
- task: DotNetCoreCLI@2
  displayName: 'Restore packages'
  inputs:
    command: 'restore'
    projects: '**/*.csproj'

- task: DotNetCoreCLI@2
  displayName: 'Build'
  inputs:
    command: 'build'
    projects: '**/*.csproj'
    arguments: '--configuration $(buildConfiguration)'

- task: DotNetCoreCLI@2
  displayName: 'Run tests'
  inputs:
    command: 'test'
    projects: '**/*Tests/*.csproj'
    arguments: '--configuration $(buildConfiguration) --collect "Code coverage"'
```

#### Pipeline Frontend (Node.js)
```yaml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: NodeTool@0
  inputs:
    versionSpec: '20.x'
  displayName: 'Install Node.js'

- script: |
    npm ci
    npm run lint
    npm run test
    npm run build
  displayName: 'npm install, lint, test and build'
```

### Validações Obrigatórias

#### Quality Gates
- **Cobertura de testes**: Mínimo 80%
- **Lint**: Zero erros de linting
- **Security**: Scan de vulnerabilidades
- **Performance**: Testes de carga quando aplicável

#### Branch Protection
- **Revisão obrigatória**: Pelo menos 1 aprovação
- **Status checks**: Todos os testes devem passar
- **Histórico linear**: Evitar merge commits desnecessários
- **Delete após merge**: Limpeza automática de branches

### Ferramentas de Qualidade

#### .NET
- **SonarQube**: Análise estática e cobertura
- **OWASP Dependency Check**: Vulnerabilidades em dependências
- **Benchmark.NET**: Testes de performance

#### Frontend
- **ESLint**: Linting JavaScript/TypeScript
- **Prettier**: Formatação automática
- **Lighthouse CI**: Performance e acessibilidade
- **npm audit**: Vulnerabilidades em pacotes
