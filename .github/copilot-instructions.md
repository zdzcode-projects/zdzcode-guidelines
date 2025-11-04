# Copilot Instructions for ZDZCode Guidelines

## Repository Overview

This repository contains comprehensive guidelines and standards for the ZDZCode development ecosystem. It serves as the central source of truth for coding practices, architecture patterns, and development workflows across all ZDZCode projects.

## Technology Stack

- **Backend**: ASP.NET Core 8/9 (.NET 8/9) with Clean Architecture
- **Frontend**: Vue 3 + Nuxt 3 + Vuetify 3
- **Database**: SQL Server (on-premises and Azure SQL Database)
- **Package Manager**: Yarn
- **Node.js**: v20.12.2 (Iron)
- **CI/CD**: Azure DevOps Pipelines
- **Project Management**: Azure Boards
- **Version Control**: Azure Repos (Git)

## Guidelines for AI Assistance

### Architecture Patterns
- Follow Clean Architecture principles with Domain, Application, Infrastructure, and API layers
- Use Entity Framework Core with proper async patterns (`ToListAsync()`)
- Implement dependency injection using .NET's native container
- Apply CQRS and Repository patterns where appropriate

### Code Style and Conventions
- **C# Backend**: Use PascalCase for public members, camelCase for private fields with underscore prefix
- **JavaScript/TypeScript**: Use camelCase for variables and functions, PascalCase for components
- **Database**: Follow entity naming conventions as defined in `docs/guia-entidades.md`
- **Services**: Follow variable naming patterns from `docs/nomenclatura-variaveis-servico.md`

### Branch and Commit Conventions
- Branch format: `<type>/<short-description>`
  - `feature/`: new functionality
  - `fix/` or `bugfix/`: bug corrections
  - `docs/`: documentation changes
  - `chore/`: maintenance tasks
- Commit messages: Short, imperative mood, max 72 characters on first line
- Reference the `docs/guia-de-commits.md` for detailed conventions

### Frontend Development
- Use Vue 3 Composition API with `ref()` for primitives and `reactive()` for objects
- Implement layouts dynamically for different application sections
- Organize components by domain and ensure reusability
- Use Pinia for global state management
- Apply ESLint (Flat Config) and Prettier for code quality

### Security and Best Practices
- Use HTTPS for all communications
- Implement proper authentication and authorization
- Protect against CSRF and XSS attacks
- Follow Azure DevOps security best practices with Zero Trust principles
- Use HttpClientFactory for efficient HTTP client management

### Testing
- **Backend**: Use xUnit or NUnit for unit and integration tests
- **Frontend**: Use Vitest or Jest for unit tests, Cypress for E2E tests
- Configure Azure DevOps pipelines for automated testing

### Documentation Standards
- Maintain detailed README.md files for each project
- Use `docs/` folder for additional guides and references
- Keep documentation updated with architecture and practice changes
- Follow the patterns established in this repository's documentation

### Example Projects Reference
The repository references several example projects that should be used as templates:
- **ZDZCode.Data.EntityFramework**: Base data access library
- **ZDZCode.OMS.Linx**: Linx API implementation
- **ZDZCode.Payments.Marvin**: Marvin API implementation
- **ZDZCode.ProjectManagement.AzureDevops**: Azure DevOps API integration
- **ZDZCode.Tests.Core**: Centralized testing utilities
- **OMS.Domain**: Domain library example
- **OMS.API**: API implementation example
- **ZDZCode.NuxtKit**: Frontend component library
- **OMS.WEB**: Frontend application example

### When Providing Code Suggestions
1. Always consider the Clean Architecture structure
2. Use async/await patterns appropriately
3. Include proper error handling and logging
4. Follow the established naming conventions
5. Consider security implications
6. Provide comments only when necessary for complex logic
7. Suggest appropriate testing approaches
8. Reference relevant documentation when available

### Repository-Specific Notes
- This is a documentation repository - focus on maintaining consistency across all guideline documents
- When suggesting changes to documentation, ensure they align with the existing structure in `docs/padroes-e-diretrizes.md`
- Consider the Portuguese language context for documentation
- Maintain the professional tone and comprehensive nature of existing documentation

For detailed information on specific topics, refer to the comprehensive documentation in the `docs/` folder and the `AGENTS.md` file.