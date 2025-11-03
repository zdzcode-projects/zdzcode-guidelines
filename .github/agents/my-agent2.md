---
name: Asaas Library
description: Implementa a biblioteca Asaas Library C#
---

# My Agent

# Guia de Implementação para Agente de IA - ZDZCode.Payments.Asaas

## Sobre o Projeto

Este projeto é uma biblioteca de integração com a API de pagamentos do Asaas, seguindo os padrões e arquitetura da **ZDZCode**. A biblioteca está construída em **.NET 8.0** e utiliza C#.

## Arquitetura e Padrões ZDZCode

### Estrutura de Classes Parciais
O projeto utiliza **classes parciais** para organizar endpoints por domínio. Cada conjunto de funcionalidades tem sua própria classe parcial:

- `AsaasService.cs` - Classe base principal
- `AsaasService_Invoice.cs` - Endpoints de Notas Fiscais
- `AsaasService_AccountDocument.cs` - Endpoints de Documentos de Conta
- `AsaasService_PaymentDunning.cs` - Endpoints de Negativação
- `AsaasService_Subaccount.cs` - Endpoints de Subcontas
- E outras classes parciais por domínio

### Herança e Base Classes
```csharp
public partial class AsaasService : HttpServiceBase
```

A classe `AsaasService` herda de `HttpServiceBase` (da biblioteca `ZC.DomainService.HttpClientCore`) que fornece métodos HTTP base como:
- `GetAsync<T>()`
- `PostAsync<T>()`
- `PutAsync<T>()`
- `DeleteAsync<T>()`

### Estrutura de DTOs
Os DTOs seguem a estrutura:
```
DTO/
├── [Dominio]/
│   ├── Request/
│   │   └── [Dominio]Request.cs (herda de BaseAsaasRequest)
│   └── Response/
│       └── [Dominio]Response.cs
```

Exemplo:
```csharp
namespace ZDZCode.Payments.Asaas.DTO.Invoice.Request
{
    public class InvoiceRequest : BaseAsaasRequest
    {
        // Propriedades
    }
}
```

### Configuração HTTP
- **BaseUrl**: `https://api.asaas.com` (Produção) ou `https://sandbox.asaas.com` (Sandbox)
- **UrlPrefix**: `/v3` (Produção) ou `/api/v3` (Sandbox)
- **Autenticação**: Via header `access_token` usando variável de ambiente `ASAAS_TOKEN`
- **Serialização**: CamelCase para requests, com formato de data `yyyy-MM-dd`

### Padrão de Respostas
Utiliza `ListPageResponse<T>` para listas paginadas:
```csharp
public async Task<ListPageResponse<InvoiceResponse>> ListInvoices(BaseAsaasRequest request = null)
{
    return await GetAsync<ListPageResponse<InvoiceResponse>>("/invoices", request);
}
```

## Estrutura de Testes

### Organização dos Testes
Os testes seguem a mesma estrutura de classes parciais:

```
ZDZCode.Payments.Asaas.Tests/
├── AsaasServiceTests_Accountinfo.cs
├── AsaasServiceTests_Anticipation.cs
├── AsaasServiceTests_Finance.cs
├── AsaasServiceTests_PaymentDocument.cs
└── StartupHost.cs
```

### Configuração de Testes
```csharp
public class StartupHost : ZCStartupAPI
{
    public override void ConfigureIntegrationServices(IServiceCollection services)
    {
        services.AddScoped<AsaasService>();
    }
}
```

### Padrão de Testes
```csharp
using FluentAssertions;
using ZC.Tests.Core;

namespace ZDZCode.Payments.Asaas.Tests
{
    [Collection(nameof(AsaasCollection))]
    public partial class AsaasServiceTests : TestServiceBase<StartupHost>
    {
        private readonly AsaasService _asaasService;

        public AsaasServiceTests(AsaasFixture fixture)
        {
            _asaasService = fixture.GetService<AsaasService>();
        }

        [Fact]
        public async Task DeveConsultarInformacoesDaConta()
        {
            // Arrange
            
            // Act
            var resultado = await _asaasService.GetAccountInfo();
            
            // Assert
            resultado.Should().NotBeNull();
        }
    }
}
```

## Instruções para Implementação

### 1. Implementar Novo Endpoint

Para adicionar um novo endpoint, siga estes passos:

#### Passo 1: Criar DTOs
```csharp
// DTO/[NomeDominio]/Request/[NomeDominio]Request.cs
namespace ZDZCode.Payments.Asaas.DTO.[NomeDominio].Request
{
    public class [NomeDominio]Request : BaseAsaasRequest
    {
        public string Propriedade1 { get; set; }
        public int Propriedade2 { get; set; }
        // Adicione outras propriedades conforme API Asaas
    }
}

// DTO/[NomeDominio]/Response/[NomeDominio]Response.cs
namespace ZDZCode.Payments.Asaas.DTO.[NomeDominio].Response
{
    public class [NomeDominio]Response
    {
        public string Id { get; set; }
        public string Status { get; set; }
        // Adicione outras propriedades conforme API Asaas
    }
}
```

#### Passo 2: Criar Classe Parcial do Service
```csharp
// AsaasService_[NomeDominio].cs
using ZC.DomainService.HttpClientCore;
using ZDZCode.Payments.Asaas.Commum;
using ZDZCode.Payments.Asaas.DTO.[NomeDominio].Request;
using ZDZCode.Payments.Asaas.DTO.[NomeDominio].Response;

namespace ZDZCode.Payments.Asaas
{
    public partial class AsaasService : HttpServiceBase
    {
        /// <summary>
        /// [Descrição do que o método faz]
        /// </summary>
        public async Task<[NomeDominio]Response> Create[NomeDominio]([NomeDominio]Request request)
        {
            return await PostAsync<[NomeDominio]Response>("/endpoint", request);
        }

        /// <summary>
        /// Lista [recursos]
        /// </summary>
        public async Task<ListPageResponse<[NomeDominio]Response>> List[NomeDominio]s(BaseAsaasRequest request = null)
        {
            return await GetAsync<ListPageResponse<[NomeDominio]Response>>("/endpoint", request);
        }

        /// <summary>
        /// Obtém [recurso] pelo identificador
        /// </summary>
        public async Task<[NomeDominio]Response> Get[NomeDominio]ById(string id, BaseAsaasRequest request = null)
        {
            return await GetAsync<[NomeDominio]Response>($"/endpoint/{id}", request);
        }

        /// <summary>
        /// Atualiza [recurso]
        /// </summary>
        public async Task<[NomeDominio]Response> Update[NomeDominio](string id, [NomeDominio]Request request)
        {
            return await PutAsync<[NomeDominio]Response>($"/endpoint/{id}", request);
        }

        /// <summary>
        /// Remove [recurso]
        /// </summary>
        public async Task<ValueResponse> Delete[NomeDominio](string id, BaseAsaasRequest request = null)
        {
            return await DeleteAsync<ValueResponse>($"/endpoint/{id}", request);
        }
    }
}
```

### 2. Implementar Testes Unitários

#### Passo 1: Criar Classe de Testes Parcial
```csharp
// AsaasServiceTests_[NomeDominio].cs
using FluentAssertions;
using ZC.DomainService.HttpClientCore;
using ZDZCode.Payments.Asaas.DTO.[NomeDominio].Request;
using ZDZCode.Payments.Asaas.Tests.Common;

namespace ZDZCode.Payments.Asaas.Tests
{
    public partial class AsaasServiceTests
    {
        [Fact]
        public async Task DeveCriar[NomeDominio]ComSucesso()
        {
            // Arrange
            var request = new [NomeDominio]Request
            {
                Propriedade1 = "valor teste",
                Propriedade2 = 123
            };

            // Act
            var resultado = await _asaasService.Create[NomeDominio](request);

            // Assert
            resultado.Should().NotBeNull();
            resultado.Id.Should().NotBeNullOrEmpty();
            resultado.Status.Should().NotBeNullOrEmpty();
        }

        [Fact]
        public async Task DeveListar[NomeDominio]sComSucesso()
        {
            // Arrange
            var request = new BaseAsaasRequest
            {
                Offset = 0,
                Limit = 10
            };

            // Act
            var resultado = await _asaasService.List[NomeDominio]s(request);

            // Assert
            resultado.Should().NotBeNull();
            resultado.Data.Should().NotBeNull();
        }

        [Fact]
        public async Task DeveObter[NomeDominio]PorIdComSucesso()
        {
            // Arrange
            var id = "id_teste_existente";

            // Act
            var resultado = await _asaasService.Get[NomeDominio]ById(id);

            // Assert
            resultado.Should().NotBeNull();
            resultado.Id.Should().Be(id);
        }

        [Fact]
        public async Task DeveAtualizar[NomeDominio]ComSucesso()
        {
            // Arrange
            var id = "id_teste_existente";
            var request = new [NomeDominio]Request
            {
                Propriedade1 = "valor atualizado"
            };

            // Act
            var resultado = await _asaasService.Update[NomeDominio](id, request);

            // Assert
            resultado.Should().NotBeNull();
            resultado.Id.Should().Be(id);
        }

        [Fact]
        public async Task DeveRemover[NomeDominio]ComSucesso()
        {
            // Arrange
            var id = "id_teste_existente";

            // Act
            var resultado = await _asaasService.Delete[NomeDominio](id);

            // Assert
            resultado.Should().NotBeNull();
            resultado.Value.Should().BeTrue();
        }
    }
}
```

## Endpoints Identificados para Implementação

Com base na estrutura do projeto, aqui estão os domínios que já possuem estrutura mas podem precisar de endpoints adicionais:

1. **Anticipation** (Antecipação) - Classe de teste vazia
2. **Finance** (Finanças) - Classe de teste vazia
3. **PaymentDocument** (Documentos de Pagamento) - Classe de teste vazia
4. **MobilePhoneRecharge** (Recarga de Celular) - Pastas criadas mas vazias

## Checklist de Implementação

### Para Cada Novo Endpoint:
- [ ] Criar DTOs de Request (herdando de `BaseAsaasRequest`)
- [ ] Criar DTOs de Response
- [ ] Criar classe parcial `AsaasService_[Dominio].cs`
- [ ] Implementar métodos com documentação XML (`<summary>`)
- [ ] Seguir padrão de nomenclatura: `Create`, `List`, `GetById`, `Update`, `Delete`
- [ ] Usar tipos de retorno apropriados (`Task<T>` ou `Task<ListPageResponse<T>>`)

### Para Cada Teste:
- [ ] Criar classe parcial `AsaasServiceTests_[Dominio].cs`
- [ ] Herdar de `TestServiceBase<StartupHost>`
- [ ] Injetar `AsaasService` via construtor
- [ ] Usar `FluentAssertions` para assertions
- [ ] Nomear testes com padrão `Deve[Acao][Resultado]`
- [ ] Seguir estrutura AAA (Arrange, Act, Assert)
- [ ] Adicionar atributo `[Fact]` para cada teste

## Padrões de Nomenclatura

### Métodos de Service:
- `Create[Entidade]` - POST
- `List[Entidades]` - GET (lista)
- `Get[Entidade]ById` - GET (único)
- `Update[Entidade]` - PUT
- `Delete[Entidade]` - DELETE
- `[Acao][Entidade]` - Para ações específicas (ex: `CancelInvoice`)

### Métodos de Teste:
- `Deve[Acao][Resultado]` (ex: `DeveCriarClienteComSucesso`)
- `NaoDeve[Acao]Quando[Condicao]` (ex: `NaoDeveCriarClienteQuandoCpfInvalido`)

## Variáveis de Ambiente Necessárias

```bash
ASAAS_TOKEN=sua_api_key_aqui
ASPNETCORE_ENVIRONMENT=Development # ou Production
```

## Bibliotecas e Dependências ZDZCode

- `ZC.DomainService.HttpClientCore` - Base para serviços HTTP
- `ZDZCode.Infrastructure` - Infraestrutura base
- `ZDZCode.ExternalServices` - Serviços externos
- `ZC.Tests.Core` - Base para testes
- `FluentAssertions` - Assertions fluentes para testes
- `AutoFixture` - Geração de dados de teste
- `Moq` - Framework de mocking

## Exemplo Completo: Implementando Endpoint de Antecipação

### 1. DTOs
```csharp
// DTO/Anticipation/Request/AnticipationRequest.cs
namespace ZDZCode.Payments.Asaas.DTO.Anticipation.Request
{
    public class AnticipationRequest : BaseAsaasRequest
    {
        public string Payment { get; set; }
        public decimal? Value { get; set; }
        public string BillingType { get; set; }
    }
}

// DTO/Anticipation/Response/AnticipationResponse.cs
namespace ZDZCode.Payments.Asaas.DTO.Anticipation.Response
{
    public class AnticipationResponse
    {
        public string Id { get; set; }
        public string Status { get; set; }
        public decimal Value { get; set; }
        public string DateCreated { get; set; }
    }
}
```

### 2. Service
```csharp
// AsaasService_Anticipation.cs
using ZC.DomainService.HttpClientCore;
using ZDZCode.Payments.Asaas.Commum;
using ZDZCode.Payments.Asaas.DTO.Anticipation.Request;
using ZDZCode.Payments.Asaas.DTO.Anticipation.Response;

namespace ZDZCode.Payments.Asaas
{
    public partial class AsaasService : HttpServiceBase
    {
        /// <summary>
        /// Simula uma antecipação de recebíveis.
        /// </summary>
        public async Task<AnticipationResponse> SimulateAnticipation(AnticipationRequest request)
        {
            return await PostAsync<AnticipationResponse>("/anticipations/simulate", request);
        }

        /// <summary>
        /// Solicita uma antecipação de recebíveis.
        /// </summary>
        public async Task<AnticipationResponse> RequestAnticipation(AnticipationRequest request)
        {
            return await PostAsync<AnticipationResponse>("/anticipations", request);
        }

        /// <summary>
        /// Lista as antecipações.
        /// </summary>
        public async Task<ListPageResponse<AnticipationResponse>> ListAnticipations(BaseAsaasRequest request = null)
        {
            return await GetAsync<ListPageResponse<AnticipationResponse>>("/anticipations", request);
        }

        /// <summary>
        /// Obtém uma antecipação pelo identificador.
        /// </summary>
        public async Task<AnticipationResponse> GetAnticipationById(string id, BaseAsaasRequest request = null)
        {
            return await GetAsync<AnticipationResponse>($"/anticipations/{id}", request);
        }
    }
}
```

### 3. Testes
```csharp
// AsaasServiceTests_Anticipation.cs
using FluentAssertions;
using ZC.DomainService.HttpClientCore;
using ZDZCode.Payments.Asaas.DTO.Anticipation.Request;

namespace ZDZCode.Payments.Asaas.Tests
{
    public partial class AsaasServiceTests
    {
        [Fact]
        public async Task DeveSimularAntecipacaoComSucesso()
        {
            // Arrange
            var request = new AnticipationRequest
            {
                Payment = "pay_123456789",
                BillingType = "BOLETO"
            };

            // Act
            var resultado = await _asaasService.SimulateAnticipation(request);

            // Assert
            resultado.Should().NotBeNull();
            resultado.Value.Should().BeGreaterThan(0);
        }

        [Fact]
        public async Task DeveSolicitarAntecipacaoComSucesso()
        {
            // Arrange
            var request = new AnticipationRequest
            {
                Payment = "pay_123456789",
                BillingType = "BOLETO"
            };

            // Act
            var resultado = await _asaasService.RequestAnticipation(request);

            // Assert
            resultado.Should().NotBeNull();
            resultado.Id.Should().NotBeNullOrEmpty();
            resultado.Status.Should().NotBeNullOrEmpty();
        }

        [Fact]
        public async Task DeveListarAntecipacoesComSucesso()
        {
            // Arrange
            var request = new BaseAsaasRequest
            {
                Offset = 0,
                Limit = 10
            };

            // Act
            var resultado = await _asaasService.ListAnticipations(request);

            // Assert
            resultado.Should().NotBeNull();
            resultado.Data.Should().NotBeNull();
        }

        [Fact]
        public async Task DeveObterAntecipacaoPorIdComSucesso()
        {
            // Arrange
            var id = "ant_123456789";

            // Act
            var resultado = await _asaasService.GetAnticipationById(id);

            // Assert
            resultado.Should().NotBeNull();
            resultado.Id.Should().Be(id);
        }
    }
}
```

## Notas Importantes

1. **Sempre use comentários XML** (`<summary>`) em português para documentar métodos
2. **Siga a convenção de nomes** estabelecida no projeto
3. **Use classes parciais** para manter a organização por domínio
4. **Todos os Request DTOs** devem herdar de `BaseAsaasRequest`
5. **Use `FluentAssertions`** para assertions nos testes
6. **Mantenha a estrutura de pastas** consistente
7. **Configure o ambiente** corretamente (Sandbox vs Produção)
8. **Valide os dados** conforme a documentação da API Asaas
9. **Trate Rate Limiting** - O serviço já possui suporte para isso na classe base

## Referências

- Documentação API Asaas: https://docs.asaas.com/
- Sandbox Asaas: https://sandbox.asaas.com/
