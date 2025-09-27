# Padrões e Diretrizes

Este documento descreve boas práticas e padrões recomendados para projetos mantidos pela comunidade ZDZCode. Ele serve como guia abrangente para desenvolvimento, cobrindo desde arquitetura até práticas de qualidade e segurança.

Para convenções específicas de commits e branches, consulte o documento [Guia de Commits](guia-de-commits.md).

## 🛠️ Stack Tecnológica

### Backend
- **Framework**: ASP.NET Core 8 e 9 (.NET 8/9)
- **Arquitetura**: Clean Architecture
- **Banco de Dados**: SQL Server (on-premises e Azure SQL Database)
- **ORM**: Entity Framework Core
- **Testes**: xUnit ou NUnit para testes unitários e de integração

### Frontend
- **Framework**: Vue 3 + Nuxt 3 + Vuetify 3
- **Build Tool**: Vite
- **Gerenciamento de Estado**: Pinia
- **Node.js**: v20.12.2 (Iron)
- **Gerenciador de Pacotes**: Yarn
- **Testes**: Vitest ou Jest para testes unitários, Cypress para testes end-to-end

### Ferramentas de Desenvolvimento
- **CI/CD**: Azure DevOps Pipelines
- **Gerenciamento de Projetos**: Azure Boards
- **Controle de Versão**: Azure Repos (Git)
- **Qualidade de Código**: ESLint (Flat Config), Prettier, SonarQube

## ⚙️ Arquitetura Backend (.NET Core 8/9)

### Estrutura do Projeto

Adotamos a **Clean Architecture**, segmentando o projeto em camadas bem definidas:

```
src/
├── Domain/          # Entidades, Value Objects, Interfaces de domínio
├── Application/     # Use Cases, DTOs, Interfaces de aplicação
├── Infrastructure/  # Implementações de acesso a dados, APIs externas
└── API/            # Controllers, Middleware, Configurações
```

Esta estrutura promove:
- **Separação de responsabilidades**
- **Testabilidade** 
- **Manutenibilidade**
- **Independência de frameworks**

### Práticas Recomendadas

#### Injeção de Dependência
- Utilize o sistema nativo do .NET Core para registro de dependências
- Prefira interfaces para abstrair implementações
- Configure serviços no `Program.cs` ou `Startup.cs`

#### Programação Assíncrona
- Evite chamadas bloqueantes síncronos
- Prefira `async/await` para operações de I/O
- Utilize `ConfigureAwait(false)` em bibliotecas
- Implemente `CancellationToken` em operações longas

#### Acesso a Dados
- Utilize Entity Framework Core com padrão Repository/Unit of Work quando necessário
- Prefira `ToListAsync()`, `FirstOrDefaultAsync()` para evitar bloqueios
- Implemente paginação para grandes conjuntos de dados usando `IAsyncEnumerable<T>`
- Configure connection strings de forma segura

#### Tratamento de Erros
- Implemente middleware global para tratamento de exceções
- Use logging estruturado com Serilog ou Application Insights
- Retorne códigos HTTP apropriados
- Não exponha detalhes internos em mensagens de erro

#### Performance
- Evite alocações desnecessárias de objetos grandes
- Utilize HttpClientFactory para reutilização de conexões HTTP
- Implemente caching quando apropriado
- Configure compression para responses HTTP

### Ferramentas e Recursos

- **HttpClientFactory**: Gerenciamento eficiente de HttpClient
- **Serilog / Application Insights**: Logging estruturado e monitoramento
- **OpenAPI / Swagger**: Documentação automática de APIs
- **FluentValidation**: Validação de modelos de entrada
- **AutoMapper**: Mapeamento entre DTOs e entidades

## 🎨 Frontend (Vue 3 + Nuxt 3 + Vuetify 3)

### Estrutura do Projeto

```
pages/              # Roteamento automático baseado em arquivos
components/         # Componentes Vue reutilizáveis
layouts/            # Layouts de página
composables/        # Composables personalizados
plugins/            # Plugins do Nuxt
middleware/         # Middleware de rotas
stores/             # Stores do Pinia
assets/             # Assets estáticos (CSS, imagens)
public/             # Arquivos públicos
```

### Práticas Recomendadas

#### Composição API
- Prefira Composition API sobre Options API
- Use `ref()` para valores primitivos
- Use `reactive()` para objetos complexos
- Extraia lógica reutilizável em composables

#### Componentes
- Organize componentes em pastas por domínio/funcionalidade
- Use PascalCase para nomes de componentes
- Implemente props tipadas com TypeScript
- Prefira composables para lógica compartilhada

#### Gerenciamento de Estado
- Utilize Pinia para estado global
- Mantenha estado local nos componentes quando possível
- Normalize dados complexos nas stores
- Implemente actions assíncronas apropriadas

#### Estilização
- Utilize Vuetify 3 para componentes UI consistentes
- Configure temas globais no Vuetify
- Use CSS Modules ou scoped styles
- Implemente design responsivo

#### Performance
- Aproveite o SSR/SSG do Nuxt 3
- Implemente lazy loading para rotas e componentes
- Otimize imagens e assets
- Use code splitting automático do Vite

### Ferramentas e Configurações

#### Auto Import
- Configure `unplugin-auto-import` para APIs do Vue
- Auto-importe composables personalizados
- Configure imports para bibliotecas terceiras

#### Qualidade de Código
- Configure ESLint com Flat Config
- Use Prettier para formatação automática
- Implemente pre-commit hooks
- Configure TypeScript para tipagem forte

### Estrutura de Repositórios

1. Utilize um `README.md` com objetivo do projeto e instruções básicas.
2. Adote uma convenção de nomes consistente para branches e commits.
3. Mantenha arquivos de configuração e scripts de automação em pastas dedicadas.
4. Configure `.gitignore` apropriado para a stack tecnológica
5. Inclua documentação técnica na pasta `docs/`
6. Mantenha dependências atualizadas e documentadas

## 🧪 Testes e Qualidade

### Backend (.NET)

#### Testes Unitários
- **Framework**: xUnit ou NUnit
- **Mocking**: Moq ou NSubstitute
- **Cobertura**: Mínimo 80% de cobertura de código
- Organize testes espelhando a estrutura do projeto
- Use padrão AAA (Arrange, Act, Assert)
- Nomeie testes descritivamente: `MethodName_Scenario_ExpectedResult`

#### Testes de Integração
- Teste endpoints de API completos
- Use `TestServer` para testes in-memory
- Configure banco de dados de teste isolado
- Teste fluxos críticos de negócio

### Frontend (Vue/Nuxt)

#### Testes Unitários
- **Framework**: Vitest ou Jest
- **Testing Library**: @vue/test-utils
- Teste lógica de componentes isoladamente
- Mock dependências externas
- Teste composables separadamente

#### Testes End-to-End
- **Framework**: Cypress ou Playwright
- Teste fluxos completos de usuário
- Configure dados de teste consistentes
- Teste em diferentes resoluções/dispositivos

### Práticas Gerais de Qualidade

#### Análise Estática
- Configure linters apropriados (ESLint, StyleCop)
- Use formatadores automáticos (Prettier, EditorConfig)
- Implemente análise de código com SonarQube
- Configure pre-commit hooks

#### Performance
- Monitore performance de APIs
- Implemente testes de carga quando necessário
- Configure alertas de performance
- Otimize queries de banco de dados

## 🔐 Segurança

### Backend
- **HTTPS**: Utilize HTTPS em todas as comunicações
- **Autenticação**: Implemente JWT ou ASP.NET Identity
- **Autorização**: Configure políticas granulares de acesso
- **Validação**: Valide todas as entradas do usuário
- **Headers de Segurança**: Configure headers HSTS, CSP, X-Frame-Options
- **CORS**: Configure políticas restritivas de CORS
- **Proteção CSRF**: Implemente tokens anti-forgery
- **SQL Injection**: Use parametrização em queries
- **Secrets**: Armazene secrets no Azure Key Vault

### Frontend
- **XSS Protection**: Sanitize entradas do usuário (DOMPurify)
- **CSP**: Configure Content Security Policy apropriada
- **Token Storage**: Armazene tokens de forma segura (httpOnly cookies)
- **HTTPS**: Force redirecionamento para HTTPS
- **Dependências**: Mantenha dependências atualizadas
- **Bundle Analysis**: Analise bundles para vazamentos de dados

### Azure DevOps
- **Zero Trust**: Verifique todas as solicitações de acesso
- **Branch Policies**: Configure políticas de proteção para branches principais
- **Secret Scanning**: Configure scanning de secrets nos repositórios
- **Code Scanning**: Implemente SAST/DAST nos pipelines
- **Access Control**: Use grupos e permissões granulares
- **Audit Logs**: Configure logs de auditoria

## 🔄 Branches e Fluxo de Trabalho

### Estratégia de Branches
- Mantenha uma branch principal chamada `main`
- Utilize o formato `<tipo>/<descricao-curta>` ao nomear branches
- **Tipos de branches**:
  - `feature/`: para novas funcionalidades
  - `fix/` ou `bugfix/`: para correções de bugs
  - `docs/`: para alterações na documentação
  - `chore/`: para tarefas de manutenção
  - `refactor/`: para refatorações de código
  - `test/`: para adição/melhoria de testes
- Evite nomes longos ou genéricos
- Use kebab-case para descrições: `feature/user-authentication`

### Proteção de Branches
- Configure branch policies no Azure DevOps
- Requeira pull request reviews
- Execute testes automatizados antes do merge
- Bloqueie push direto para `main`

## 🚀 Integração Contínua / Entrega Contínua

### Azure DevOps Pipelines

#### Build Pipeline
```yaml
# Exemplo de pipeline .NET
trigger:
  branches:
    include:
    - main
    - develop

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: DotNetCoreCLI@2
  displayName: 'Restore'
  inputs:
    command: 'restore'
    
- task: DotNetCoreCLI@2
  displayName: 'Build'
  inputs:
    command: 'build'
    
- task: DotNetCoreCLI@2
  displayName: 'Test'
  inputs:
    command: 'test'
    publishTestResults: true
```

#### Práticas de CI/CD
- **Builds automáticos**: Trigger em commits para branches principais
- **Testes automatizados**: Execute testes unitários e de integração
- **Code Quality**: Integrate SonarQube ou ferramentas similares
- **Security Scanning**: Inclua análise de vulnerabilidades
- **Artifact Management**: Publique artifacts em Azure Artifacts
- **Environment Promotion**: Use stages para ambientes (dev, staging, prod)
- **Rollback Strategy**: Mantenha estratégia de rollback definida

### Scripts de Automação
Configure scripts comuns no `package.json` (Frontend):
```json
{
  "scripts": {
    "dev": "nuxt dev",
    "build": "nuxt build",
    "preview": "nuxt preview",
    "lint": "eslint .",
    "lint:fix": "eslint . --fix",
    "format": "prettier --write .",
    "test": "vitest",
    "test:coverage": "vitest --coverage"
  }
}
```
## 💾 Acesso a Dados

### Entity Framework Core

#### Configuração
- Configure DbContext com injeção de dependência
- Use migrations para controle de schema
- Configure connection strings por ambiente
- Implemente retry policies para resiliência

#### Práticas Recomendadas
- Use padrão Repository quando necessário
- Implemente Unit of Work para transações complexas
- Configure lazy loading com cuidado
- Use `AsNoTracking()` para queries read-only
- Implemente soft delete quando apropriado

#### Performance
- Configure índices adequados
- Use `Include()` para eager loading consciente
- Implemente paginação eficiente
- Configure connection pooling
- Use compiled queries para queries frequentes

### SQL Server
- Use stored procedures para lógica complexa apenas quando necessário
- Configure backup e recovery adequados
- Implemente monitoring de performance
- Use Always Encrypted para dados sensíveis
- Configure High Availability quando necessário

## 📁 Projetos de Exemplo ZDZCode

Os seguintes projetos servem como referência para implementação:

### Class Libraries C#
- **ZDZCode.Data.EntityFramework**: Biblioteca base para acesso a dados
- **ZDZCode.OMS.Linx**: Implementação da API da Linx
- **ZDZCode.Payments.Marvin**: Implementação da API da Marvin
- **ZDZCode.ProjectManagement.AzureDevops**: Integração com Azure DevOps
- **ZDZCode.Tests.Core**: Biblioteca de centralização de testes
- **OMS.Domain**: Biblioteca de domínio do projeto OMS

### APIs C#
- **OMS.API**: API principal do projeto OMS

### Frontend
- **ZDZCode.NuxtKit**: Componentes centralizados de frontend
- **OMS.WEB**: Frontend da aplicação OMS

### Utilização dos Exemplos
- Use como base para novos projetos
- Siga os padrões arquiteturais estabelecidos
- Adapte para necessidades específicas do projeto
- Mantenha consistência com as práticas documentadas

## 💻 Estilo de Código

### C# (.NET)
- Siga as convenções do C# (PascalCase para métodos e propriedades)
- Use var quando o tipo é óbvio
- Configure EditorConfig para consistência
- Use nullable reference types (.NET 6+)
- Implemente interfaces explicitamente quando apropriado
- Prefira expressions quando melhoram legibilidade

### JavaScript/TypeScript
- Use ESLint com configuração Flat Config
- Configure Prettier para formatação automática
- Prefira const > let > var
- Use arrow functions para callbacks simples
- Implemente tipagem TypeScript rigorosa
- Use destructuring quando apropriado

### Padrões Gerais
- Prefira código limpo e autocomentado
- Use nomes descritivos para variáveis e métodos
- Mantenha funções/métodos pequenos e focados
- Evite aninhamento excessivo (max 3-4 níveis)
- Documente APIs públicas
- Remove código morto regularmente
## Commits

- Escreva mensagens de commit claras, objetivas e no imperativo (ex: "Adiciona funcionalidade X" em vez de "Adicionando funcionalidade X" ou "Funcionalidade X adicionada").
- As mensagens devem ser em português.
- Limite a primeira linha da mensagem a 72 caracteres.
- Utilize parágrafos adicionais para fornecer detalhes quando necessário.
- Agrupe alterações relacionadas em um único commit sempre que possível.

## Pull Requests

- Descreva de forma objetiva o que está sendo alterado no Pull Request.
- Inclua referência a issues quando aplicável.
- Mantenha o escopo do PR focado em uma única tarefa.

## Revisão de Código

- Todo Pull Request (PR) deve ser revisado por pelo menos uma pessoa.
- Solicite revisão e, ao criar o PR, comente trechos de código que mereçam atenção especial.
- Durante a revisão, utilize comentários construtivos e objetivos.
- Aprove mudanças somente após garantir que os testes automatizados estejam passando e que as discussões tenham sido resolvidas.

## Issues

- Registre bugs, sugestões e propostas por meio de issues.
- Descreva claramente o problema, incluindo passos para reproduzir (no caso de bugs) ou os resultados esperados (para sugestões/propostas).
- Utilize labels para organizar e priorizar as issues.
- Quando possível, associe pull requests às issues correspondentes.
## Licença

Os documentos seguem a licença MIT, presente neste repositório.

## 📚 Documentação

### Estrutura da Documentação
- **README.md**: Visão geral, setup e instruções básicas
- **docs/**: Documentação técnica detalhada
- **CHANGELOG.md**: Histórico de mudanças
- **API Documentation**: Swagger/OpenAPI para APIs
- **Architecture Decision Records (ADRs)**: Para decisões arquiteturais importantes

### Boas Práticas
- Mantenha documentação atualizada
- Use diagramas para explicar arquitetura complexa
- Documente APIs públicas automaticamente
- Inclua exemplos de uso
- Documente configurações de ambiente
- Mantenha guias de troubleshooting

### Ferramentas Recomendadas
- **Markdown**: Para documentação geral
- **Mermaid**: Para diagramas
- **OpenAPI/Swagger**: Para documentação de APIs
- **DocFX**: Para documentação .NET
- **VitePress**: Para sites de documentação Vue/Nuxt

## 📖 Recursos Adicionais

### Microsoft/Azure
- [ASP.NET Core Best Practices](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/best-practices)
- [Azure DevOps Best Practices](https://learn.microsoft.com/en-us/azure/devops/organizations/security/security-best-practices)
- [Entity Framework Core Best Practices](https://learn.microsoft.com/en-us/ef/core/miscellaneous/configuring-dbcontext)

### Frontend
- [Vue 3 Best Practices](https://vuejs.org/style-guide/)
- [Nuxt 3 Documentation](https://nuxt.com/docs)
- [Vuetify 3 Guide](https://vuetifyjs.com/en/getting-started/installation/)

### Desenvolvimento
- [Clean Code Principles](https://github.com/ryanmcdermott/clean-code-javascript)
- [SOLID Principles](https://medium.com/backticks-tildes/the-s-o-l-i-d-principles-in-pictures-b34ce2f1e898)
- [Conventional Commits](https://www.conventionalcommits.org/)

### Segurança
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Azure Security Best Practices](https://learn.microsoft.com/en-us/azure/security/fundamentals/best-practices-and-patterns)
