# 👨‍💻 Orientações para Desenvolvedores ZDZCode

Este documento oferece orientações práticas para desenvolvedores trabalhando no ecossistema **ZDZCode**, complementando os [Padrões e Diretrizes](padroes-e-diretrizes.md) e o [Guia de Commits](guia-de-commits.md).

## 📋 Índice

- [Projetos de Referência](#projetos-de-referência)
- [Fluxo de Trabalho](#fluxo-de-trabalho)
- [Configuração do Ambiente](#configuração-do-ambiente)
- [Boas Práticas por Stack](#boas-práticas-por-stack)
- [Documentação](#documentação)

---

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

---

## Fluxo de Trabalho

### 1. Planejamento da Tarefa

#### Issue Tracking
- **Consulte** Azure Boards para tarefas priorizadas
- **Crie issue** no GitHub se necessário com template apropriado
- **Estime complexidade** e tempo necessário
- **Identifique dependências** com outras tarefas

#### Análise Técnica  
- **Revise** documentação de arquitetura existente
- **Identifique** componentes afetados pela mudança
- **Planeje** testes necessários
- **Considere** impactos de performance e segurança

### 2. Configuração da Branch

```bash
# Atualizar branch principal
git checkout main
git pull origin main

# Criar branch seguindo convenções
git checkout -b feature/implementar-dashboard-vendas

# Configurar upstream
git push -u origin feature/implementar-dashboard-vendas
```

### 3. Desenvolvimento Iterativo

#### Ciclo de Desenvolvimento
1. **Implementar** pequena parte da funcionalidade
2. **Testar** localmente
3. **Commit** com mensagem descritiva
4. **Push** frequente para backup
5. **Repetir** até conclusão

#### Exemplo de Commits Incrementais
```bash
feat: criar estrutura base do dashboard de vendas
feat: implementar consulta de dados de vendas mensais  
feat: adicionar gráficos interativos com Chart.js
test: cobrir cenários de filtros por período
docs: documentar API de vendas no README
```

### 4. Qualidade e Testes

#### Backend (.NET)
```bash
# Executar testes
dotnet test --collect:"XPlat Code Coverage"

# Verificar cobertura
dotnet tool install -g dotnet-reportgenerator-globaltool
reportgenerator -reports:"**/*.xml" -targetdir:"coverage"
```

#### Frontend (Vue/Nuxt)
```bash
# Linting e formatação
npm run lint
npm run format

# Testes unitários
npm run test

# Testes e2e
npm run test:e2e
```

### 5. Pull Request

#### Pre-checklist
- [ ] Código testado localmente
- [ ] Linting sem erros
- [ ] Testes automatizados passando
- [ ] Documentação atualizada
- [ ] Branch atualizada com main

#### Criação do PR
1. **Título** descritivo seguindo padrões de commit
2. **Descrição** completa com contexto e instruções de teste
3. **Reviewers** - pelo menos 1 pessoa qualificada
4. **Labels** apropriadas para categorização
5. **Milestone** se aplicável

### 6. Deploy e Monitoramento

#### Após Merge
- **Monitore** pipeline de deploy
- **Verifique** logs de aplicação
- **Teste** funcionalidade em staging/produção
- **Documente** release notes se significativo

---

## Configuração do Ambiente

### Ferramentas Essenciais

#### Desenvolvimento
- **Visual Studio Code** ou **Visual Studio 2022**
- **Git** com configuração de usuário
- **Node.js** v20.12.2 (Iron) via nvm
- **.NET 8 SDK** ou superior

#### Extensões Recomendadas (VSCode)
```json
{
  "recommendations": [
    "ms-dotnettools.csharp",
    "vue.volar",
    "bradlc.vscode-tailwindcss",
    "esbenp.prettier-vscode",
    "ms-vscode.vscode-eslint",
    "ms-azuretools.vscode-azuredevops"
  ]
}
```

### Configuração de Projeto

#### .NET Project Setup
```bash
# Criar solução
dotnet new sln -n MeuProjeto

# Criar projetos seguindo Clean Architecture
dotnet new classlib -n MeuProjeto.Domain
dotnet new classlib -n MeuProjeto.Application  
dotnet new classlib -n MeuProjeto.Infrastructure
dotnet new webapi -n MeuProjeto.API

# Adicionar à solução
dotnet sln add **/*.csproj
```

#### Nuxt Project Setup
```bash
# Criar projeto
npx nuxi@latest init meu-projeto-web
cd meu-projeto-web

# Instalar dependências essenciais
npm install @nuxtjs/tailwindcss @pinia/nuxt
npm install -D @nuxt/eslint @nuxtjs/color-mode

# Configurar Vuetify
npm install vuetify @mdi/font
```

---

## Boas Práticas por Stack

### Backend (.NET)

#### Estrutura de Pastas
```
src/
├── MeuProjeto.Domain/
│   ├── Entities/          # Entidades de domínio
│   ├── ValueObjects/      # Objetos de valor
│   ├── Interfaces/        # Contratos do domínio
│   └── Exceptions/        # Exceções específicas
├── MeuProjeto.Application/
│   ├── UseCases/          # Casos de uso
│   ├── DTOs/              # Data Transfer Objects
│   ├── Interfaces/        # Interfaces de aplicação
│   └── Validators/        # Validações FluentValidation
├── MeuProjeto.Infrastructure/
│   ├── Data/              # Entity Framework
│   ├── Services/          # Implementações de serviços
│   └── External/          # APIs externas
└── MeuProjeto.API/
    ├── Controllers/       # Controllers REST
    ├── Middleware/        # Middlewares customizados
    └── Configuration/     # Configurações startup
```

#### Injeção de Dependência
```csharp
// Program.cs - .NET 8
builder.Services.AddScoped<IUserRepository, UserRepository>();
builder.Services.AddScoped<IUserService, UserService>();
builder.Services.AddTransient<IEmailService, EmailService>();
```

### Frontend (Vue/Nuxt)

#### Estrutura de Componentes
```
components/
├── UI/                    # Componentes base reutilizáveis
│   ├── BaseButton.vue
│   ├── BaseModal.vue
│   └── BaseForm.vue
├── Layout/                # Componentes de layout
│   ├── Header.vue
│   ├── Sidebar.vue
│   └── Footer.vue  
└── Features/              # Componentes específicos
    ├── UserProfile/
    ├── ProductCatalog/
    └── ShoppingCart/
```

#### Composables
```javascript
// composables/useApi.js
export const useApi = () => {
  const config = useRuntimeConfig()
  
  const $fetch = $fetch.create({
    baseURL: config.public.apiBase,
    headers: {
      'Content-Type': 'application/json'
    }
  })
  
  return { $fetch }
}
```

---

## Documentação

### Documentação de Código

#### Backend (.NET)
```csharp
/// <summary>
/// Serviço responsável pelo gerenciamento de usuários
/// </summary>
public class UserService : IUserService
{
    /// <summary>
    /// Cria um novo usuário no sistema
    /// </summary>
    /// <param name="createUserDto">Dados do usuário a ser criado</param>
    /// <returns>Usuário criado com ID gerado</returns>
    /// <exception cref="ValidationException">Dados inválidos</exception>
    public async Task<UserDto> CreateUserAsync(CreateUserDto createUserDto)
    {
        // Implementação...
    }
}
```

#### Frontend (Vue)
```vue
<template>
  <!-- 
    Componente para exibição de lista de produtos
    Suporta filtros, paginação e ordenação
  -->
  <div class="product-list">
    <!-- Template... -->
  </div>
</template>

<script setup lang="ts">
/**
 * Componente ProductList
 * 
 * @description Lista produtos com funcionalidades de filtro
 * @example
 * <ProductList 
 *   :filters="{ category: 'electronics' }"
 *   @product-selected="handleSelection"  
 * />
 */

interface Props {
  /** Filtros aplicados à lista */
  filters?: ProductFilters
  /** Modo de exibição (grid ou list) */
  viewMode?: 'grid' | 'list'
}
</script>
```

### README de Projeto

Cada projeto deve ter um README seguindo este template:

````markdown
# Nome do Projeto

Breve descrição do projeto e seu propósito.

## 🚀 Tecnologias

- [ASP.NET Core 8](https://docs.microsoft.com/aspnet/core/) - Framework web
- [Entity Framework Core](https://docs.microsoft.com/ef/core/) - ORM
- [Vue 3](https://vuejs.org/) - Framework frontend
- [Nuxt 3](https://nuxt.com/) - Meta-framework Vue

## 📋 Pré-requisitos

- .NET 8.0 SDK
- Node.js 20.12+ 
- SQL Server 2019+
- Visual Studio Code ou Visual Studio 2022

## 🔧 Instalação

### Backend
```bash
cd src/MeuProjeto.API
dotnet restore
dotnet run
```

### Frontend  
```bash
cd frontend
npm install
npm run dev
```

## ⚡ Uso

### Executar Localmente
```bash
# Backend (porta 5000)
dotnet run --project src/MeuProjeto.API

# Frontend (porta 3000)  
npm run dev
```

### Build de Produção
```bash
# Backend
dotnet publish -c Release

# Frontend
npm run build
```

## 🧪 Testes

```bash
# Backend
dotnet test

# Frontend
npm run test
npm run test:e2e
```

## 📁 Estrutura

```
├── src/
│   ├── Domain/           # Regras de negócio
│   ├── Application/      # Casos de uso
│   ├── Infrastructure/   # Dados e serviços externos
│   └── API/             # Controllers e setup
├── frontend/
│   ├── components/      # Componentes Vue
│   ├── pages/           # Páginas da aplicação
│   └── stores/          # Estados globais Pinia
└── docs/               # Documentação adicional
```

## 🚀 Deploy

### Ambiente de Desenvolvimento
- URL: https://dev-app.zdzcode.com
- Pipeline: Azure DevOps

### Produção  
- URL: https://app.zdzcode.com
- Deploy: Manual via Azure Portal

## 🤝 Contribuindo

1. Fork o projeto
2. Crie uma branch feature (`git checkout -b feature/nova-funcionalidade`)
3. Commit suas mudanças (`git commit -m 'feat: adicionar nova funcionalidade'`)
4. Push para a branch (`git push origin feature/nova-funcionalidade`)
5. Abra um Pull Request

Ver [Guia de Contribuição](CONTRIBUTING.md) para mais detalhes.

## 📝 Licença

Este projeto está sob a licença MIT - veja [LICENSE](LICENSE) para detalhes.

## 📞 Contato

- **Equipe**: desenvolvimento@zdzcode.com
- **Issues**: [GitHub Issues](https://github.com/user/repo/issues)
- **Slack**: #projeto-nome
````

### Manutenção da Documentação

1. **Atualização Regular**
   - Revisar documentação a cada release
   - Atualizar exemplos e screenshots
   - Verificar links e referências

2. **Versionamento**
   - Manter changelog atualizado
   - Documentar breaking changes
   - Versionar documentação junto com código

3. **Acessibilidade**
   - Usar linguagem clara e objetiva
   - Incluir exemplos práticos
   - Organizar informação hierarquicamente

---

## 📚 Recursos Adicionais

### Documentação Interna
- **[Padrões e Diretrizes](./padroes-e-diretrizes.md)** - Base fundamental
- **[Guia de Commits](./guia-de-commits.md)** - Convenções de versionamento
- **[Guia de Entidades](./guia-entidades.md)** - Padrões backend
- **[Guia para Agentes](../AGENTS.md)** - Automação e IA

### Links Úteis
- **[Azure DevOps](https://dev.azure.com/zdzcode)** - Gerenciamento de projetos
- **[Confluence](https://zdzcode.atlassian.net)** - Documentação técnica
- **[Slack](https://zdzcode.slack.com)** - Comunicação da equipe

---

*Última atualização: Dezembro 2024 • Versão 2.0*
