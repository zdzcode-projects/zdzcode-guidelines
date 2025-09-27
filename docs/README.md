# 📚 Documentos de Padrões e Diretrizes ZDZCode

Este diretório contém a documentação completa para orientar projetos do ecossistema **ZDZCode**. Todos os documentos seguem padrões consistentes de formatação e terminologia para facilitar o entendimento e aplicação.

## 🎯 Documentos Principais

### Diretrizes Gerais
- **[Padrões e Diretrizes](./padroes-e-diretrizes.md)** - Recomendações gerais sobre estrutura de repositórios, estilo de código, convenções para branches, commits, issues, pull requests, revisão de código, documentação e integração contínua
- **[Guia de Commits](./guia-de-commits.md)** - Regras para mensagens de commit e nomenclatura de branches

### Desenvolvimento
- **[Orientações para Desenvolvedores](./orientacoes-desenvolvedores.md)** - Fluxo de trabalho e exemplos de projetos do ecossistema ZDZCode

### Padrões de Código

#### Backend (.NET)
- **[Guia de Entidades](./guia-entidades.md)** - Passo a passo para criar e mapear entidades compatíveis com ZDZCode.Data.EntityFramework
- **[Nomenclatura de Propriedades](./nomenclatura-propriedades-entidade.md)** - Padrões para nomear propriedades em classes de entidade
- **[Nomenclatura de Variáveis](./nomenclatura-variaveis-servico.md)** - Convenções para nomear variáveis em classes de serviço

## 🔄 Navegação Rápida

| Tema | Documento | Descrição |
|------|-----------|-----------|
| 🏗️ **Estrutura** | [Padrões e Diretrizes](./padroes-e-diretrizes.md) | Base fundamental para todos os projetos |
| 📝 **Git/GitHub** | [Guia de Commits](./guia-de-commits.md) | Convenções de versionamento |
| 👨‍💻 **Desenvolvimento** | [Orientações](./orientacoes-desenvolvedores.md) | Fluxo de trabalho e exemplos |
| 🏛️ **Entidades** | [Guia de Entidades](./guia-entidades.md) | Criação e mapeamento ORM |
| 🏷️ **Nomenclatura** | [Propriedades](./nomenclatura-propriedades-entidade.md) • [Variáveis](./nomenclatura-variaveis-servico.md) | Padrões de nomeação |

## 📋 Stack Tecnológica

Esta documentação cobre projetos que utilizam:

- **Backend**: ASP.NET Core 8/9, Entity Framework Core, SQL Server
- **Frontend**: Vue 3, Nuxt 3, Vuetify 3, TypeScript
- **DevOps**: Azure DevOps, Azure Boards, Azure Repos
- **Qualidade**: ESLint, Prettier, xUnit/NUnit, Vitest/Jest

## 🚀 Como Usar

1. **Novos Projetos**: Comece com [Padrões e Diretrizes](./padroes-e-diretrizes.md)
2. **Convenções Git**: Consulte [Guia de Commits](./guia-de-commits.md)
3. **Backend .NET**: Siga [Guia de Entidades](./guia-entidades.md) e padrões de nomenclatura
4. **Fluxo de Trabalho**: Veja [Orientações para Desenvolvedores](./orientacoes-desenvolvedores.md)

## 🤝 Contribuindo

Para contribuir com melhorias nesta documentação:

1. Siga as próprias diretrizes descritas nos documentos
2. Mantenha consistência de formatação e terminologia
3. Teste exemplos de código antes de documentar
4. Solicite revisão através de Pull Request

---

💡 **Dica**: Para agentes de IA trabalhando com estes projetos, consulte também o [Guia para Agentes](../AGENTS.md) que contém informações técnicas detalhadas sobre a stack e práticas recomendadas.
