# Guia de Trabalho para Desenvolvedores ZDZCode

Este documento reúne orientações básicas para trabalhar nos projetos do ecossistema **ZDZCode**. Utilize-o em conjunto com o [Padrões e Diretrizes](padroes-e-diretrizes.md) e o [Guia de Commits](guia-de-commits.md).

## Projetos de Referência

Os projetos listados abaixo servem como referência para implementação de padrões. Consulte o [Guia para Agentes](../AGENTS.md) para detalhes técnicos completos sobre cada projeto:

### Bibliotecas Base (.NET)
- **ZDZCode.Data.EntityFramework**: Biblioteca para acesso a dados com Entity Framework
- **ZDZCode.Tests.Core**: Utilitários centralizados para testes

### Integrações e APIs
- **ZDZCode.OMS.Linx**: Implementação da API da Linx
- **ZDZCode.Payments.Marvin**: Implementação da API da Marvin  
- **ZDZCode.ProjectManagement.AzureDevops**: Integração com Azure DevOps

### Projeto OMS (Exemplo Completo)
- **OMS.Domain**: Biblioteca de domínio seguindo Clean Architecture
- **OMS.API**: API REST principal com documentação OpenAPI
- **OMS.WEB**: Frontend Vue 3 + Nuxt 3

### Frontend Compartilhado
- **ZDZCode.NuxtKit**: Componentes e utilitários compartilhados para projetos Nuxt

## Fluxo de Trabalho

1. **Criação de Branch**  
   Siga as convenções descritas em [Guia de Commits](guia-de-commits.md). Utilize prefixos como `feature/` ou `bugfix/`.
2. **Desenvolvimento**  
   - Para projetos .NET, mantenha a estrutura `src/Domain`, `Application`, `Infrastructure` e `API`.
   - Para projetos frontend, utilize Vue 3 + Nuxt 3 e siga as práticas de lint e formatação com ESLint e Prettier.
3. **Commits**  
   Escreva mensagens curtas no imperativo, limitando a primeira linha a 72 caracteres. Consulte o [Guia de Commits](guia-de-commits.md) para exemplos.
4. **Pull Requests**  
   Mantenha um escopo focado e descreva claramente o que está sendo alterado. Sempre solicite revisão de pelo menos uma pessoa.
5. **Integração Contínua**  
   Configure pipelines no Azure DevOps para executar testes e validar qualidade de código.

## Documentação

- Mantenha um `README.md` em cada projeto explicando objetivo e passos de uso.
- Utilize a pasta `docs/` para manuais adicionais e exemplos.
- Atualize este repositório com novas diretrizes sempre que a stack ou os processos mudarem.
