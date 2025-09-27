# 🧠 Guia para Agentes IA - ZDZCode

Este documento serve como guia para agentes de IA que interagem com o ecossistema de desenvolvimento ZDZCode. Ele define práticas recomendadas, arquitetura e ferramentas utilizadas para garantir consistência, desempenho e escalabilidade em nossos projetos.

## 📌 Diretrizes para Agentes

### Princípios Fundamentais

- **Consistência**: Assegurar que todos os documentos sigam a estrutura e terminologia padronizadas
- **Atualização Contínua**: Manter os documentos atualizados conforme mudanças na arquitetura ou nas práticas da equipe
- **Clareza e Objetividade**: Utilizar linguagem clara, evitando jargões desnecessários, para facilitar o entendimento por todos os stakeholders
- **Referências**: Incluir referências a documentos externos ou internos relevantes que complementem as informações apresentadas

### Projetos de Referência

Os seguintes projetos servem como base para gerar documentação de padrões:

#### Bibliotecas C# (.NET)
- **ZDZCode.Data.EntityFramework**: Biblioteca base para acesso a dados
- **ZDZCode.OMS.Linx**: Implementação da API da Linx
- **ZDZCode.Payments.Marvin**: Implementação da API da Marvin
- **ZDZCode.ProjectManagement.AzureDevops**: Integração com Azure DevOps
- **ZDZCode.Tests.Core**: Biblioteca de centralização de testes
- **OMS.Domain**: Biblioteca de domínio do projeto OMS

#### APIs
- **OMS.API**: API principal do projeto OMS

#### Frontend
- **ZDZCode.NuxtKit**: Componentes centralizados de frontend
- **OMS.WEB**: Interface web da OMS

---

## 🛠️ Stack Tecnológica

### Tecnologias Principais

- **Backend**: ASP.NET Core 8 e 9 (.NET 8/9)
- **Frontend**: Vue 3 + Nuxt 3 + Vuetify 3
- **Banco de Dados**: SQL Server (on-premises e Azure SQL Database)
- **Gerenciador de Pacotes**: Yarn
- **Node.js**: v20.12.2 (Iron)

### Ferramentas de DevOps

- **CI/CD**: Azure DevOps Pipelines
- **Gerenciamento de Projetos**: Azure Boards
- **Controle de Versão**: Azure Repos (Git)
- **Ferramentas de Qualidade**: ESLint (Flat Config), Prettier, Pinia, Unplugin Auto Import

*Referências*: [Deploy to Azure SQL Database](https://learn.microsoft.com/en-us/azure/devops/pipelines/targets/azure-sqldb?view=azure-devops), [Azure SQL Development with Azure DevOps](https://stackoverflow.com/questions/62865225/what-are-the-standards-of-azure-sql-development-with-azure-devops)

---

## ⚙️ Backend (.NET Core 8/9)

### Arquitetura

Adotamos a **Clean Architecture**, organizando o projeto em camadas bem definidas:

```
src/
├── Domain/        # Entidades e regras de negócio
├── Application/   # Casos de uso e interfaces
├── Infrastructure/# Implementações técnicas
└── API/          # Controllers e configurações
```

Esta estrutura promove separação de responsabilidades, facilitando testes e manutenção.

### Práticas Recomendadas

#### Performance e Escalabilidade
- **Injeção de Dependência**: Utilize o sistema nativo do .NET para promover baixo acoplamento
- **Programação Assíncrona**: Evite chamadas bloqueantes; prefira `async/await` para operações de I/O
- **Paginação**: Implemente paginação para grandes conjuntos de dados, utilizando `IAsyncEnumerable<T>` quando apropriado
- **Gerenciamento de Recursos**: Evite alocações de objetos grandes em caminhos críticos de código
- **Acesso a Dados**: Utilize Entity Framework Core com `ToListAsync()` para evitar bloqueios síncronos

#### Arquitetura e Organização
- **Middleware**: Implemente middlewares para tratamento de erros, logging e autenticação
- **Segurança**: Utilize HTTPS, políticas de CORS e autenticação baseada em JWT ou Identity

### Ferramentas e Recursos

- **HttpClientFactory**: Para reutilização eficiente de instâncias de HttpClient
- **Serilog / Application Insights**: Para logging estruturado e monitoramento
- **OpenAPI / Swagger**: Para documentação e testes de APIs

---

## 🎨 Frontend (Vue 3 + Nuxt 3 + Vuetify 3)

### Estrutura do Projeto

- **Layouts**: Layouts dinâmicos para diferentes seções da aplicação
- **Componentes**: Organizados em pastas por domínio e reutilizáveis
- **Roteamento**: Baseado em arquivos com suporte a rotas aninhadas e dinâmicas
- **Gerenciamento de Estado**: Pinia para gerenciamento global de estado

### Práticas Recomendadas

#### Desenvolvimento
- **Composição API**: Prefira `ref()` para primitivas e `reactive()` para objetos
- **Auto Importação**: Utilize `unplugin-auto-import` para importar APIs automaticamente
- **Estilização**: Centralize estilos globais e temas no Vuetify
- **Formatação e Linting**: Configure Prettier e ESLint para manter a consistência do código

#### Performance
- **SSR**: Aproveite o Server-Side Rendering do Nuxt 3
- **Code Splitting**: Utilize a divisão de código automática do Nuxt 3
- **Otimização de Imagens**: Use componentes otimizados do Nuxt

### Ferramentas e Recursos

- **Vuetify 3**: Componentes UI com Material Design
- **Vite**: Bundler para build rápido e eficiente
- **Nuxt DevTools**: Inspeção e depuração de aplicações Nuxt

---

## 🧪 Testes e Qualidade

### Estratégias de Teste

#### Backend (.NET)
- **Testes Unitários**: xUnit ou NUnit para lógica de negócio isolada
- **Testes de Integração**: Validação de APIs e acesso a dados
- **Testes de Performance**: Benchmarks com BenchmarkDotNet

#### Frontend (Vue/Nuxt)
- **Testes Unitários**: Vitest ou Jest para componentes e lógica
- **Testes End-to-End**: Cypress para fluxos completos de usuário
- **Testes de Componentes**: Vue Test Utils para validação de UI

#### Integração Contínua
- **Azure DevOps Pipelines**: Execução automática de testes e deploys
- **Quality Gates**: Cobertura mínima de código e análise estática
- **Feedback Rápido**: Notificações imediatas sobre falhas

---

## 📦 Convenções e Configurações

### Versionamento e Dependências

- **Node.js**: v20.12.2 (Iron) - definido em `.nvmrc`
- **Package Manager**: Yarn com `engine-strict=true` no `.npmrc`
- **Semantic Versioning**: Utilizar versionamento semântico para releases

### Scripts Padronizados

#### Frontend
- `dev`: Inicia servidor de desenvolvimento
- `build`: Gera build de produção otimizada
- `lint`: Executa linter e correções automáticas
- `format`: Formata código com Prettier
- `test`: Executa suite de testes

#### Backend
- `restore`: Restaura dependências NuGet
- `build`: Compila a solução
- `test`: Executa testes unitários e de integração
- `publish`: Gera artefatos para deploy

---

## 🔐 Segurança

### Backend Security

#### Comunicação e Autenticação
- **HTTPS**: Obrigatório em todas as comunicações
- **Autenticação Robusta**: JWT tokens ou ASP.NET Core Identity
- **Autorização**: Políticas baseadas em roles e claims

#### Proteções Essenciais
- **CSRF Protection**: Anti-forgery tokens em formulários
- **XSS Prevention**: Sanitização de entrada e Content Security Policy
- **SQL Injection**: Uso de parametrized queries e Entity Framework
- **Rate Limiting**: Proteção contra ataques de força bruta

### Frontend Security

#### Validação e Sanitização
- **Input Sanitization**: Validação rigorosa de entradas do usuário
- **XSS Prevention**: Escape de conteúdo dinâmico
- **Token Security**: Armazenamento seguro de tokens de autenticação

#### Políticas de Segurança
- **CORS**: Configuração apropriada de políticas Cross-Origin
- **CSP**: Content Security Policy para prevenir XSS
- **HTTPS Only**: Todas as comunicações através de HTTPS

### Azure DevOps Security

#### Princípios de Segurança
- **Zero Trust**: Verificação de todas as solicitações de acesso
- **Least Privilege**: Permissões mínimas necessárias
- **Compliance**: Políticas de segurança e conformidade

#### Proteções de Infraestrutura
- **Azure Firewall**: Proteção de rede perimetral
- **DDoS Protection**: Mitigação de ataques distribuídos
- **Security Scanning**: Análise automatizada de vulnerabilidades

*Referência*: [Security Best Practices - Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/organizations/security/security-best-practices?view=azure-devops)

---

## 📚 Recursos Adicionais

### Documentação Oficial

#### .NET e ASP.NET Core
- [ASP.NET Core Best Practices](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/best-practices?view=aspnetcore-9.0)
- [Guia para Construir Backends Seguros em ASP.NET Core](https://slashdev.io/-guide-to-building-secure-backends-in-asp-net-core-in-2024)

#### Vue.js e Nuxt
- [Setting Up Nuxt 3 with Vuetify 3](https://dev.to/sarveshk76/setting-up-nuxt-3-with-vuetify-3-a-comprehensive-guide-gmg)
- [Vue 3 Tips & Best Practices](https://medium.com/js-dojo/vue-3-tips-best-practices-54aec91d95dc)

#### Azure DevOps
- [Práticas Recomendadas de Integração - Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/integrate/concepts/integration-bestpractices?view=azure-devops)
- [Implantação no Azure SQL Database](https://learn.microsoft.com/en-us/azure/devops/pipelines/targets/azure-sqldb?view=azure-devops)
- [Práticas de Segurança - Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/organizations/security/security-best-practices?view=azure-devops)

### Documentação Interna

Para diretrizes específicas do ZDZCode, consulte:
- [Padrões e Diretrizes](./docs/padroes-e-diretrizes.md)
- [Guia de Commits](./docs/guia-de-commits.md)
- [Guia de Entidades](./docs/guia-entidades.md)

---

## 📝 Manutenção

Este documento é atualizado regularmente para refletir as melhores práticas e ferramentas mais recentes utilizadas pela equipe ZDZCode.

**Última atualização**: Dezembro 2024  
**Versão**: 2.0