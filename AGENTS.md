Claro! A seguir está a versão atualizada do arquivo `agents.md` para o seu Codex, incorporando o uso do Azure DevOps, Azure Boards e SQL Server como parte da sua stack Microsoft.

* * *

🧠 agents.md
============

📌 Diretrizes para o Agente
---------------------------

*   **Consistência**: Assegurar que todos os documentos sigam a estrutura e terminologia padronizadas.
    
*   **Atualização Contínua**: Manter os documentos atualizados conforme mudanças na arquitetura ou nas práticas da equipe.
    
*   **Clareza e Objetividade**: Utilizar linguagem clara, evitando jargões desnecessários, para facilitar o entendimento por todos os stakeholders.
    
*   **Referências**: Incluir referências a documentos externos ou internos relevantes que complementem as informações apresentadas.[awari.com.br](https://awari.com.br/documento-de-arquitetura-de-software-exemplo-e-estrutura-para-a-documentacao-adequada/?utm_source=chatgpt.com)



#Projetos de exemplo na pasta exemplos
Utilize estes projetos de exemplo como base auxiliar para vc gerar a documentação de padrõs
#Class library C#

Biblioteca base da ZDZCode para acesso a dados: ZDZCode.Data.EntityFramework
Implementação de api da Linx: ZDZCode.OMS.Linx
Implementação da api da Marvin: ZDZCode.Payments.Marvin
Implementação da api do Azure Devops: ZDZCode.ProjectManagement.AzureDevops
Biblioteca de centralização de testes: ZDZCode.Tests.Core

Biblioteca de dominio do projeto OMS: OMS.Domain

#API em C#

API do projeto OMS: OMS.API

#Frontend
Projeto centralizador de componentes de frontend: ZDZCode.NuxtKit
Projeto de frontend da OMS: OMS.WEB



Visão Geral
-----------

Este documento serve como guia para agentes que interagem com nosso ecossistema de desenvolvimento. Ele descreve nossas práticas recomendadas, arquitetura e ferramentas utilizadas para garantir consistência, desempenho e escalabilidade em nossos projetos.

* * *

🛠️ Stack Tecnológica
---------------------

*   **Backend**: ASP.NET Core 8 e 9 (.NET 8/9)
    
*   **Frontend**: Vue 3 + Nuxt 3 + Vuetify 3
    
*   **Banco de Dados**: SQL Server (on-premises e Azure SQL Database)
    
*   **Gerenciador de Pacotes**: Yarn
    
*   **Node.js**: v20.12.2 (Iron)
    
*   **CI/CD**: Azure DevOps Pipelines
    
*   **Gerenciamento de Projetos**: Azure Boards
    
*   **Controle de Versão**: Azure Repos (Git)
    
*   **Ferramentas de Qualidade**: ESLint (Flat Config), Prettier, Pinia, Unplugin Auto Import([learn.microsoft.com](https://learn.microsoft.com/en-us/azure/devops/pipelines/targets/azure-sqldb?view=azure-devops&utm_source=chatgpt.com "Deploy to Azure SQL Database - Azure Pipelines | Microsoft Learn"), [stackoverflow.com](https://stackoverflow.com/questions/62865225/what-are-the-standards-of-azure-sql-development-with-azure-devops?utm_source=chatgpt.com "What are the standards of Azure Sql development with Azure DevOps?"))
    

* * *

⚙️ Backend (.NET Core 8/9)
--------------------------

### Arquitetura

Adotamos a **Clean Architecture**, segmentando o projeto em:

    
    src/
    ├── Domain/
    ├── Application/
    ├── Infrastructure/
    └── API/
    

Essa estrutura promove separação de responsabilidades, facilitando testes e manutenção.

### Práticas Recomendadas

*   **Injeção de Dependência**: Utilize o sistema nativo do .NET para promover baixo acoplamento.
    
*   **Programação Assíncrona**: Evite chamadas bloqueantes; prefira `async/await` para operações de I/O.
    
*   **Paginação**: Implemente paginação para grandes conjuntos de dados, utilizando `IAsyncEnumerable<T>` quando apropriado.
    
*   **Gerenciamento de Recursos**: Evite alocações de objetos grandes em caminhos críticos de código.
    
*   **Acesso a Dados**: Utilize Entity Framework Core com `ToListAsync()` para evitar bloqueios síncronos.
    
*   **Middleware**: Implemente middlewares para tratamento de erros, logging e autenticação.
    
*   **Segurança**: Utilize HTTPS, políticas de CORS e autenticação baseada em JWT ou Identity.
    

### Ferramentas e Recursos

*   **HttpClientFactory**: Para reutilização eficiente de instâncias de HttpClient.
    
*   **Serilog / Application Insights**: Para logging estruturado e monitoramento.
    
*   **OpenAPI / Swagger**: Para documentação e testes de APIs.
    

* * *

🎨 Frontend (Vue 3 + Nuxt 3 + Vuetify 3)
----------------------------------------

### Estrutura do Projeto

*   **Layouts**: Utilizamos layouts dinâmicos para diferentes seções da aplicação.
    
*   **Componentes**: Organizados em pastas por domínio e reutilizáveis.
    
*   **Roteamento**: Baseado em arquivos com suporte a rotas aninhadas e dinâmicas.
    
*   **Gerenciamento de Estado**: Pinia para gerenciamento global de estado.
    

### Práticas Recomendadas

*   **Composição de API**: Prefira `ref()` para primitivas e `reactive()` para objetos.
    
*   **Auto Importação**: Utilize `unplugin-auto-import` para importar APIs automaticamente.
    
*   **Estilização**: Centralize estilos globais e temas no Vuetify.
    
*   **Formatação e Linting**: Configure Prettier e ESLint para manter a consistência do código.
    
*   **Performance**: Aproveite o SSR e a divisão de código do Nuxt 3 para melhorar o desempenho.
    

### Ferramentas e Recursos

*   **Vuetify 3**: Para componentes UI com Material Design.
    
*   **Vite**: Como bundler para build rápido e eficiente.
    
*   **Nuxt DevTools**: Para inspeção e depuração de aplicações Nuxt.
    

* * *

🧪 Testes e Qualidade
---------------------

*   **Backend**: Utilize xUnit ou NUnit para testes unitários e de integração.
    
*   **Frontend**: Utilize Vitest ou Jest para testes unitários e Cypress para testes end-to-end.
    
*   **CI/CD**: Configure pipelines no Azure DevOps para execução automática de testes e deploys.
    

* * *

📦 Convenções e Configurações
-----------------------------

*   **Versionamento de Node**: Definido em `.nvmrc` com `lts/iron`.
    
*   **Gerenciador de Pacotes**: Yarn definido em `.npmrc` com `engine-strict=true`.
    
*   **Scripts Comuns**:
    *   `dev`: Inicia o servidor de desenvolvimento.
        
    *   `build`: Gera a build de produção.
        
    *   `lint`: Executa o linter.
        
    *   `format`: Formata o código com Prettier.
        

* * *

🔐 Segurança
------------

*   **Backend**:
    *   Utilize HTTPS em todas as comunicações.
        
    *   Implemente autenticação e autorização robustas.
        
    *   Proteja contra CSRF e XSS.
        
*   **Frontend**:
    *   Sanitize entradas do usuário.
        
    *   Utilize tokens seguros para autenticação.
        
    *   Implemente políticas de CORS apropriadas.
        
*   **Azure DevOps**:
    *   Adote princípios de Zero Trust para verificar todas as solicitações de acesso.
        
    *   Implemente políticas de segurança e conformidade para proteger o ambiente de desenvolvimento.
        
    *   Utilize o Azure Firewall e o Azure DDoS Protection para proteger contra ataques externos.([learn.microsoft.com](https://learn.microsoft.com/en-us/azure/devops/organizations/security/security-best-practices?view=azure-devops&utm_source=chatgpt.com "Security best practices - Azure DevOps | Microsoft Learn"))
        

* * *

📚 Recursos Adicionais
----------------------

*   [ASP.NET Core Best Practices](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/best-practices?view=aspnetcore-9.0)
    
*   [Setting Up Nuxt 3 with Vuetify 3](https://dev.to/sarveshk76/setting-up-nuxt-3-with-vuetify-3-a-comprehensive-guide-gmg)
    
*   [Vue 3 Tips & Best Practices](https://medium.com/js-dojo/vue-3-tips-best-practices-54aec91d95dc)
    
*   [Guia para Construir Backends Seguros em ASP.NET Core em 2024](https://slashdev.io/-guide-to-building-secure-backends-in-asp-net-core-in-2024)
    
*   [Práticas recomendadas de integração - Azure DevOps Services](https://learn.microsoft.com/en-us/azure/devops/integrate/concepts/integration-bestpractices?view=azure-devops)
    
*   [Implantação no Azure SQL Database - Azure Pipelines](https://learn.microsoft.com/en-us/azure/devops/pipelines/targets/azure-sqldb?view=azure-devops)
    
*   [Práticas recomendadas de segurança - Azure DevOps](https://learn.microsoft.com/en-us/azure/devops/organizations/security/security-best-practices?view=azure-devops)
    

* * *

Este documento é atualizado regularmente para refletir as melhores práticas e ferramentas mais recentes utilizadas pela equipe.

* * *