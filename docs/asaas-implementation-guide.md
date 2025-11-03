# Guia de Implementação - ZDZCode.Payments.Asaas

Este guia detalha como implementar cada módulo da biblioteca de integração com o Asaas, seguindo os padrões ZDZCode.

## 📋 Estrutura do Projeto

```
ZDZCode.Payments.Asaas/
├── src/
│   └── ZDZCode.Payments.Asaas/
│       ├── Configuration/
│       │   ├── AsaasOptions.cs
│       │   └── AsaasEndpoints.cs
│       ├── Http/
│       │   ├── IAsaasHttpClient.cs
│       │   ├── AsaasHttpClient.cs
│       │   └── Handlers/
│       │       ├── AuthenticationHandler.cs
│       │       └── LoggingHandler.cs
│       ├── Errors/
│       │   ├── AsaasApiException.cs
│       │   └── AsaasErrorResponse.cs
│       ├── Pagination/
│       │   ├── PageRequest.cs
│       │   ├── PageResponse.cs
│       │   └── PaginationExtensions.cs
│       ├── Clients/
│       │   ├── Customers/
│       │   │   ├── ICustomersClient.cs
│       │   │   ├── CustomersClient.cs
│       │   │   └── Models/
│       │   │       ├── CustomerRequest.cs
│       │   │       └── CustomerResponse.cs
│       │   ├── Payments/
│       │   ├── Subscriptions/
│       │   └── ... (outros módulos)
│       └── Extensions/
│           └── ServiceCollectionExtensions.cs
├── tests/
│   └── ZDZCode.Payments.Asaas.Tests/
│       ├── Http/
│       ├── Clients/
│       └── Integration/
├── samples/
│   └── ZDZCode.Payments.Asaas.Samples/
└── docs/
```

## 🔧 Implementação da Infraestrutura Base

### 1. AsaasOptions.cs

```csharp
using System.ComponentModel.DataAnnotations;

namespace ZDZCode.Payments.Asaas.Configuration
{
    /// <summary>
    /// Opções de configuração para o cliente Asaas.
    /// </summary>
    public class AsaasOptions
    {
        /// <summary>
        /// Chave de API do Asaas (obrigatória).
        /// Referência: https://docs.asaas.com/reference/autenticacao
        /// </summary>
        [Required(ErrorMessage = "A API Key é obrigatória")]
        public string ApiKey { get; set; } = string.Empty;

        /// <summary>
        /// Indica se deve usar o ambiente sandbox (padrão: false).
        /// Sandbox: https://sandbox.asaas.com/api/v3
        /// Produção: https://api.asaas.com/v3
        /// </summary>
        public bool UseSandbox { get; set; } = false;

        /// <summary>
        /// URL base customizada (opcional). Se não fornecida, usa baseado em UseSandbox.
        /// </summary>
        public string? BaseUrl { get; set; }

        /// <summary>
        /// Timeout para requisições HTTP (padrão: 30 segundos).
        /// </summary>
        public TimeSpan Timeout { get; set; } = TimeSpan.FromSeconds(30);

        /// <summary>
        /// Habilita retry automático para falhas transitórias (padrão: true).
        /// </summary>
        public bool EnableRetry { get; set; } = true;

        /// <summary>
        /// Número máximo de tentativas de retry (padrão: 3).
        /// </summary>
        [Range(1, 10)]
        public int MaxRetryAttempts { get; set; } = 3;

        /// <summary>
        /// User-Agent customizado para as requisições.
        /// </summary>
        public string UserAgent { get; set; } = "ZDZCode.Payments.Asaas/1.0.0";

        /// <summary>
        /// Habilita Circuit Breaker (padrão: false).
        /// </summary>
        public bool EnableCircuitBreaker { get; set; } = false;

        /// <summary>
        /// Número de falhas consecutivas para abrir o Circuit Breaker.
        /// </summary>
        public int CircuitBreakerThreshold { get; set; } = 5;

        /// <summary>
        /// Duração do Circuit Breaker aberto.
        /// </summary>
        public TimeSpan CircuitBreakerDuration { get; set; } = TimeSpan.FromMinutes(1);

        /// <summary>
        /// Obtém a URL base efetiva baseada nas configurações.
        /// </summary>
        public string GetEffectiveBaseUrl()
        {
            if (!string.IsNullOrEmpty(BaseUrl))
                return BaseUrl;

            return UseSandbox
                ? "https://sandbox.asaas.com/api/v3"
                : "https://api.asaas.com/v3";
        }
    }
}
```

### 2. AuthenticationHandler.cs

```csharp
using System.Net.Http.Headers;
using Microsoft.Extensions.Options;
using ZDZCode.Payments.Asaas.Configuration;

namespace ZDZCode.Payments.Asaas.Http.Handlers
{
    /// <summary>
    /// Handler para adicionar autenticação nas requisições.
    /// Referência: https://docs.asaas.com/reference/autenticacao
    /// </summary>
    public class AuthenticationHandler : DelegatingHandler
    {
        private readonly AsaasOptions _options;

        /// <summary>
        /// Inicializa uma nova instância do handler de autenticação.
        /// </summary>
        /// <param name="options">Opções de configuração do Asaas</param>
        public AuthenticationHandler(IOptions<AsaasOptions> options)
        {
            _options = options.Value;
        }

        /// <summary>
        /// Envia a requisição HTTP adicionando os headers de autenticação.
        /// </summary>
        /// <param name="request">Requisição HTTP</param>
        /// <param name="cancellationToken">Token de cancelamento</param>
        /// <returns>Resposta HTTP</returns>
        protected override async Task<HttpResponseMessage> SendAsync(
            HttpRequestMessage request,
            CancellationToken cancellationToken)
        {
            // Adiciona o header de autenticação
            request.Headers.Add("access_token", _options.ApiKey);
            
            // Adiciona User-Agent
            request.Headers.UserAgent.ParseAdd(_options.UserAgent);

            return await base.SendAsync(request, cancellationToken);
        }
    }
}
```

### 3. AsaasApiException.cs

```csharp
using System.Net;

namespace ZDZCode.Payments.Asaas.Errors
{
    /// <summary>
    /// Exceção lançada quando a API do Asaas retorna um erro.
    /// </summary>
    public class AsaasApiException : Exception
    {
        /// <summary>
        /// Código de status HTTP da resposta.
        /// </summary>
        public HttpStatusCode StatusCode { get; }

        /// <summary>
        /// Lista de erros retornados pela API.
        /// </summary>
        public List<AsaasError>? Errors { get; }

        /// <summary>
        /// Corpo bruto da resposta.
        /// </summary>
        public string? RawResponse { get; }

        /// <summary>
        /// Inicializa uma nova instância da exceção da API Asaas.
        /// </summary>
        /// <param name="statusCode">Código de status HTTP</param>
        /// <param name="message">Mensagem de erro</param>
        /// <param name="errors">Lista de erros detalhados</param>
        /// <param name="rawResponse">Corpo bruto da resposta HTTP</param>
        public AsaasApiException(
            HttpStatusCode statusCode,
            string message,
            List<AsaasError>? errors = null,
            string? rawResponse = null)
            : base(message)
        {
            StatusCode = statusCode;
            Errors = errors;
            RawResponse = rawResponse;
        }

        /// <summary>
        /// Retorna uma representação em string da exceção com detalhes dos erros.
        /// </summary>
        /// <returns>String formatada com detalhes da exceção</returns>
        public override string ToString()
        {
            var msg = $"AsaasApiException: {Message} (Status: {StatusCode})";
            
            if (Errors?.Any() == true)
            {
                msg += "\nErros:";
                foreach (var error in Errors)
                {
                    msg += $"\n  - [{error.Code}] {error.Description}";
                }
            }

            return msg;
        }
    }

    /// <summary>
    /// Representa um erro individual retornado pela API.
    /// </summary>
    public class AsaasError
    {
        /// <summary>
        /// Código do erro.
        /// </summary>
        public string Code { get; set; } = string.Empty;

        /// <summary>
        /// Descrição do erro.
        /// </summary>
        public string Description { get; set; } = string.Empty;
    }

    /// <summary>
    /// Modelo de resposta de erro da API Asaas.
    /// </summary>
    public class AsaasErrorResponse
    {
        /// <summary>
        /// Lista de erros retornados pela API.
        /// </summary>
        public List<AsaasError> Errors { get; set; } = new();
    }
}
```

### 4. PageRequest.cs e PageResponse.cs

```csharp
namespace ZDZCode.Payments.Asaas.Pagination
{
    /// <summary>
    /// Requisição de paginação padrão do Asaas.
    /// Referência: https://docs.asaas.com/docs/paginacao
    /// </summary>
    public class PageRequest
    {
        /// <summary>
        /// Elemento inicial da lista (padrão: 0).
        /// </summary>
        public int Offset { get; set; } = 0;

        /// <summary>
        /// Número de elementos por página (padrão: 10, máx: 100).
        /// </summary>
        public int Limit { get; set; } = 10;

        /// <summary>
        /// Converte para query string.
        /// </summary>
        public Dictionary<string, string> ToQueryParameters()
        {
            return new Dictionary<string, string>
            {
                ["offset"] = Offset.ToString(),
                ["limit"] = Limit.ToString()
            };
        }
    }

    /// <summary>
    /// Resposta paginada padrão do Asaas.
    /// </summary>
    public class PageResponse<T>
    {
        /// <summary>
        /// Indica se há mais elementos.
        /// </summary>
        public bool HasMore { get; set; }

        /// <summary>
        /// Total de elementos (pode não estar presente em todas as respostas).
        /// </summary>
        public int? TotalCount { get; set; }

        /// <summary>
        /// Número de elementos por página.
        /// </summary>
        public int Limit { get; set; }

        /// <summary>
        /// Offset atual.
        /// </summary>
        public int Offset { get; set; }

        /// <summary>
        /// Dados da página atual.
        /// </summary>
        public List<T> Data { get; set; } = new();
    }
}
```

### 5. ServiceCollectionExtensions.cs

```csharp
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Options;
using Polly;
using Polly.Extensions.Http;
using ZDZCode.Payments.Asaas.Configuration;
using ZDZCode.Payments.Asaas.Http.Handlers;
using ZDZCode.Payments.Asaas.Clients;

namespace ZDZCode.Payments.Asaas.Extensions
{
    /// <summary>
    /// Extensões para configurar o cliente Asaas no DI.
    /// </summary>
    public static class ServiceCollectionExtensions
    {
        /// <summary>
        /// Adiciona o cliente Asaas ao container de DI.
        /// </summary>
        public static IServiceCollection AddAsaas(
            this IServiceCollection services,
            Action<AsaasOptions> configureOptions)
        {
            services.Configure(configureOptions);
            return services.AddAsaasCore();
        }

        /// <summary>
        /// Adiciona o cliente Asaas ao container de DI usando IConfiguration.
        /// </summary>
        public static IServiceCollection AddAsaas(
            this IServiceCollection services,
            IConfiguration configuration)
        {
            services.Configure<AsaasOptions>(configuration);
            return services.AddAsaasCore();
        }

        private static IServiceCollection AddAsaasCore(this IServiceCollection services)
        {
            // Validação de opções
            services.AddSingleton<IValidateOptions<AsaasOptions>, AsaasOptionsValidator>();

            // Handlers
            services.AddTransient<AuthenticationHandler>();
            services.AddTransient<LoggingHandler>();

            // HttpClient com Polly
            services.AddHttpClient("Asaas")
                .AddHttpMessageHandler<AuthenticationHandler>()
                .AddHttpMessageHandler<LoggingHandler>()
                .ConfigureHttpClient((sp, client) =>
                {
                    var options = sp.GetRequiredService<IOptions<AsaasOptions>>().Value;
                    client.BaseAddress = new Uri(options.GetEffectiveBaseUrl());
                    client.Timeout = options.Timeout;
                })
                .AddPolicyHandler((sp, request) =>
                {
                    var options = sp.GetRequiredService<IOptions<AsaasOptions>>().Value;
                    
                    if (!options.EnableRetry)
                        return Policy.NoOpAsync<HttpResponseMessage>();

                    return HttpPolicyExtensions
                        .HandleTransientHttpError()
                        .OrResult(msg => msg.StatusCode == System.Net.HttpStatusCode.TooManyRequests)
                        .WaitAndRetryAsync(
                            options.MaxRetryAttempts,
                            retryAttempt => TimeSpan.FromSeconds(Math.Pow(2, retryAttempt)) + 
                                          TimeSpan.FromMilliseconds(new Random().Next(0, 1000)));
                });

            // Registrar clientes
            services.AddScoped<ICustomersClient, CustomersClient>();
            services.AddScoped<IPaymentsClient, PaymentsClient>();
            services.AddScoped<ISubscriptionsClient, SubscriptionsClient>();
            services.AddScoped<IAnticipationsClient, AnticipationsClient>();
            services.AddScoped<IInstallmentsClient, InstallmentsClient>();
            services.AddScoped<IPaymentDunningsClient, PaymentDunningsClient>();
            services.AddScoped<IInvoicesClient, InvoicesClient>();
            services.AddScoped<IPixClient, PixClient>();
            services.AddScoped<ITransfersClient, TransfersClient>();
            services.AddScoped<IPaymentLinksClient, PaymentLinksClient>();
            // ... registrar todos os outros clientes

            return services;
        }
    }

    /// <summary>
    /// Validador de opções do Asaas.
    /// </summary>
    internal class AsaasOptionsValidator : IValidateOptions<AsaasOptions>
    {
        /// <summary>
        /// Valida as opções de configuração do Asaas.
        /// </summary>
        /// <param name="name">Nome das opções</param>
        /// <param name="options">Opções a serem validadas</param>
        /// <returns>Resultado da validação</returns>
        public ValidateOptionsResult Validate(string? name, AsaasOptions options)
        {
            if (string.IsNullOrWhiteSpace(options.ApiKey))
                return ValidateOptionsResult.Fail("A API Key é obrigatória.");

            var baseUrl = options.GetEffectiveBaseUrl();
            if (!Uri.TryCreate(baseUrl, UriKind.Absolute, out var uri) || 
                uri.Scheme != Uri.UriSchemeHttps)
            {
                return ValidateOptionsResult.Fail("A URL base deve ser HTTPS.");
            }

            if (options.Timeout <= TimeSpan.Zero)
                return ValidateOptionsResult.Fail("O timeout deve ser maior que zero.");

            return ValidateOptionsResult.Success;
        }
    }
}
```

## 📝 Implementando um Cliente (Exemplo: Customers)

### 1. ICustomersClient.cs

```csharp
using ZDZCode.Payments.Asaas.Clients.Customers.Models;
using ZDZCode.Payments.Asaas.Pagination;

namespace ZDZCode.Payments.Asaas.Clients
{
    /// <summary>
    /// Cliente para gerenciamento de clientes no Asaas.
    /// Documentação: https://docs.asaas.com/reference/clientes
    /// </summary>
    public interface ICustomersClient
    {
        /// <summary>
        /// Cria um novo cliente.
        /// </summary>
        /// <param name="request">Dados do cliente</param>
        /// <param name="cancellationToken">Token de cancelamento</param>
        /// <returns>Cliente criado</returns>
        /// <see href="https://docs.asaas.com/reference/criar-novo-cliente">Documentação Oficial</see>
        Task<CustomerResponse> CreateAsync(
            CustomerRequest request,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Recupera um cliente pelo ID.
        /// </summary>
        /// <param name="id">ID do cliente</param>
        /// <param name="cancellationToken">Token de cancelamento</param>
        /// <returns>Dados do cliente</returns>
        /// <see href="https://docs.asaas.com/reference/recuperar-um-unico-cliente">Documentação Oficial</see>
        Task<CustomerResponse> GetByIdAsync(
            string id,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Lista clientes com paginação.
        /// </summary>
        /// <param name="request">Parâmetros de paginação e filtros</param>
        /// <param name="cancellationToken">Token de cancelamento</param>
        /// <returns>Página de clientes</returns>
        /// <see href="https://docs.asaas.com/reference/listar-clientes">Documentação Oficial</see>
        Task<PageResponse<CustomerResponse>> ListAsync(
            CustomerListRequest? request = null,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Itera sobre todos os clientes automaticamente (paginação automática).
        /// </summary>
        /// <param name="request">Filtros opcionais</param>
        /// <param name="cancellationToken">Token de cancelamento</param>
        /// <returns>Enumerável assíncrono de clientes</returns>
        IAsyncEnumerable<CustomerResponse> ListAllAsync(
            CustomerListRequest? request = null,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Atualiza os dados de um cliente existente.
        /// </summary>
        /// <param name="id">ID do cliente</param>
        /// <param name="request">Novos dados do cliente</param>
        /// <param name="cancellationToken">Token de cancelamento</param>
        /// <returns>Cliente atualizado</returns>
        /// <see href="https://docs.asaas.com/reference/atualizar-cliente-existente">Documentação Oficial</see>
        Task<CustomerResponse> UpdateAsync(
            string id,
            CustomerRequest request,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Remove um cliente.
        /// </summary>
        /// <param name="id">ID do cliente</param>
        /// <param name="cancellationToken">Token de cancelamento</param>
        /// <see href="https://docs.asaas.com/reference/remover-cliente">Documentação Oficial</see>
        Task DeleteAsync(
            string id,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Restaura um cliente removido.
        /// </summary>
        /// <param name="id">ID do cliente</param>
        /// <param name="cancellationToken">Token de cancelamento</param>
        /// <returns>Cliente restaurado</returns>
        /// <see href="https://docs.asaas.com/reference/restaurar-cliente">Documentação Oficial</see>
        Task<CustomerResponse> RestoreAsync(
            string id,
            CancellationToken cancellationToken = default);
    }
}
```

### 2. CustomerRequest.cs e CustomerResponse.cs

```csharp
using System.Text.Json.Serialization;

namespace ZDZCode.Payments.Asaas.Clients.Customers.Models
{
    /// <summary>
    /// Requisição para criar ou atualizar um cliente.
    /// Referência: https://docs.asaas.com/reference/criar-novo-cliente
    /// </summary>
    public class CustomerRequest
    {
        /// <summary>
        /// Nome do cliente (obrigatório).
        /// </summary>
        [JsonPropertyName("name")]
        public string Name { get; set; } = string.Empty;

        /// <summary>
        /// E-mail do cliente.
        /// </summary>
        [JsonPropertyName("email")]
        public string? Email { get; set; }

        /// <summary>
        /// Telefone do cliente.
        /// </summary>
        [JsonPropertyName("phone")]
        public string? Phone { get; set; }

        /// <summary>
        /// Telefone celular do cliente.
        /// </summary>
        [JsonPropertyName("mobilePhone")]
        public string? MobilePhone { get; set; }

        /// <summary>
        /// CPF ou CNPJ do cliente (somente números).
        /// </summary>
        [JsonPropertyName("cpfCnpj")]
        public string? CpfCnpj { get; set; }

        /// <summary>
        /// CEP do endereço.
        /// </summary>
        [JsonPropertyName("postalCode")]
        public string? PostalCode { get; set; }

        /// <summary>
        /// Endereço.
        /// </summary>
        [JsonPropertyName("address")]
        public string? Address { get; set; }

        /// <summary>
        /// Número do endereço.
        /// </summary>
        [JsonPropertyName("addressNumber")]
        public string? AddressNumber { get; set; }

        /// <summary>
        /// Complemento do endereço.
        /// </summary>
        [JsonPropertyName("complement")]
        public string? Complement { get; set; }

        /// <summary>
        /// Bairro.
        /// </summary>
        [JsonPropertyName("province")]
        public string? Province { get; set; }

        /// <summary>
        /// ID externo (para integração).
        /// </summary>
        [JsonPropertyName("externalReference")]
        public string? ExternalReference { get; set; }

        /// <summary>
        /// Desabilita envio de notificações.
        /// </summary>
        [JsonPropertyName("notificationDisabled")]
        public bool? NotificationDisabled { get; set; }

        /// <summary>
        /// Observações adicionais (máximo 500 caracteres).
        /// </summary>
        [JsonPropertyName("additionalEmails")]
        public string? AdditionalEmails { get; set; }

        /// <summary>
        /// Inscrição municipal.
        /// </summary>
        [JsonPropertyName("municipalInscription")]
        public string? MunicipalInscription { get; set; }

        /// <summary>
        /// Inscrição estadual.
        /// </summary>
        [JsonPropertyName("stateInscription")]
        public string? StateInscription { get; set; }

        /// <summary>
        /// Observações.
        /// </summary>
        [JsonPropertyName("observations")]
        public string? Observations { get; set; }

        /// <summary>
        /// Grupo do cliente.
        /// </summary>
        [JsonPropertyName("groupName")]
        public string? GroupName { get; set; }

        /// <summary>
        /// Empresa.
        /// </summary>
        [JsonPropertyName("company")]
        public string? Company { get; set; }
    }

    /// <summary>
    /// Resposta com dados do cliente.
    /// </summary>
    public class CustomerResponse
    {
        /// <summary>
        /// ID único do cliente no Asaas.
        /// </summary>
        [JsonPropertyName("id")]
        public string Id { get; set; } = string.Empty;

        /// <summary>
        /// Data de criação do cliente.
        /// </summary>
        [JsonPropertyName("dateCreated")]
        public DateTime DateCreated { get; set; }

        /// <summary>
        /// Nome do cliente.
        /// </summary>
        [JsonPropertyName("name")]
        public string Name { get; set; } = string.Empty;

        /// <summary>
        /// E-mail do cliente.
        /// </summary>
        [JsonPropertyName("email")]
        public string? Email { get; set; }

        /// <summary>
        /// Telefone do cliente.
        /// </summary>
        [JsonPropertyName("phone")]
        public string? Phone { get; set; }

        /// <summary>
        /// Telefone celular do cliente.
        /// </summary>
        [JsonPropertyName("mobilePhone")]
        public string? MobilePhone { get; set; }

        /// <summary>
        /// CPF ou CNPJ do cliente.
        /// </summary>
        [JsonPropertyName("cpfCnpj")]
        public string? CpfCnpj { get; set; }

        /// <summary>
        /// CEP do endereço.
        /// </summary>
        [JsonPropertyName("postalCode")]
        public string? PostalCode { get; set; }

        /// <summary>
        /// Logradouro do endereço.
        /// </summary>
        [JsonPropertyName("address")]
        public string? Address { get; set; }

        /// <summary>
        /// Número do endereço.
        /// </summary>
        [JsonPropertyName("addressNumber")]
        public string? AddressNumber { get; set; }

        /// <summary>
        /// Complemento do endereço.
        /// </summary>
        [JsonPropertyName("complement")]
        public string? Complement { get; set; }

        /// <summary>
        /// Bairro.
        /// </summary>
        [JsonPropertyName("province")]
        public string? Province { get; set; }

        /// <summary>
        /// Cidade.
        /// </summary>
        [JsonPropertyName("city")]
        public string? City { get; set; }

        /// <summary>
        /// Estado (UF).
        /// </summary>
        [JsonPropertyName("state")]
        public string? State { get; set; }

        /// <summary>
        /// País.
        /// </summary>
        [JsonPropertyName("country")]
        public string? Country { get; set; }

        /// <summary>
        /// Referência externa do cliente.
        /// </summary>
        [JsonPropertyName("externalReference")]
        public string? ExternalReference { get; set; }

        /// <summary>
        /// Indica se as notificações estão desabilitadas.
        /// </summary>
        [JsonPropertyName("notificationDisabled")]
        public bool NotificationDisabled { get; set; }

        /// <summary>
        /// Observações sobre o cliente.
        /// </summary>
        [JsonPropertyName("observations")]
        public string? Observations { get; set; }

        /// <summary>
        /// Indica se o cliente foi removido.
        /// </summary>
        [JsonPropertyName("deleted")]
        public bool Deleted { get; set; }
    }

    /// <summary>
    /// Filtros para listagem de clientes.
    /// </summary>
    public class CustomerListRequest : PageRequest
    {
        /// <summary>
        /// Filtrar por nome.
        /// </summary>
        [JsonPropertyName("name")]
        public string? Name { get; set; }

        /// <summary>
        /// Filtrar por e-mail.
        /// </summary>
        [JsonPropertyName("email")]
        public string? Email { get; set; }

        /// <summary>
        /// Filtrar por CPF/CNPJ.
        /// </summary>
        [JsonPropertyName("cpfCnpj")]
        public string? CpfCnpj { get; set; }

        /// <summary>
        /// Filtrar por grupo.
        /// </summary>
        [JsonPropertyName("groupName")]
        public string? GroupName { get; set; }

        /// <summary>
        /// Filtrar por referência externa.
        /// </summary>
        [JsonPropertyName("externalReference")]
        public string? ExternalReference { get; set; }
    }
}
```

### 3. CustomersClient.cs (Implementação)

```csharp
using System.Net.Http.Json;
using System.Runtime.CompilerServices;
using System.Text.Json;
using Microsoft.Extensions.Options;
using ZDZCode.Payments.Asaas.Clients.Customers.Models;
using ZDZCode.Payments.Asaas.Configuration;
using ZDZCode.Payments.Asaas.Errors;
using ZDZCode.Payments.Asaas.Pagination;

namespace ZDZCode.Payments.Asaas.Clients
{
    /// <summary>
    /// Implementação do cliente de customers do Asaas.
    /// </summary>
    public class CustomersClient : ICustomersClient
    {
        private readonly HttpClient _httpClient;
        private readonly JsonSerializerOptions _jsonOptions;

        /// <summary>
        /// Inicializa uma nova instância do cliente de customers.
        /// </summary>
        /// <param name="httpClientFactory">Factory para criação de HttpClient</param>
        public CustomersClient(IHttpClientFactory httpClientFactory)
        {
            _httpClient = httpClientFactory.CreateClient("Asaas");
            _jsonOptions = new JsonSerializerOptions
            {
                PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
                DefaultIgnoreCondition = System.Text.Json.Serialization.JsonIgnoreCondition.WhenWritingNull
            };
        }

        /// <inheritdoc />
        public async Task<CustomerResponse> CreateAsync(
            CustomerRequest request,
            CancellationToken cancellationToken = default)
        {
            var response = await _httpClient.PostAsJsonAsync(
                "/customers",
                request,
                _jsonOptions,
                cancellationToken);

            await EnsureSuccessAsync(response, cancellationToken);

            return await response.Content.ReadFromJsonAsync<CustomerResponse>(
                _jsonOptions,
                cancellationToken) ?? throw new InvalidOperationException("Resposta vazia");
        }

        /// <inheritdoc />
        public async Task<CustomerResponse> GetByIdAsync(
            string id,
            CancellationToken cancellationToken = default)
        {
            var response = await _httpClient.GetAsync(
                $"/customers/{id}",
                cancellationToken);

            await EnsureSuccessAsync(response, cancellationToken);

            return await response.Content.ReadFromJsonAsync<CustomerResponse>(
                _jsonOptions,
                cancellationToken) ?? throw new InvalidOperationException("Resposta vazia");
        }

        /// <inheritdoc />
        public async Task<PageResponse<CustomerResponse>> ListAsync(
            CustomerListRequest? request = null,
            CancellationToken cancellationToken = default)
        {
            request ??= new CustomerListRequest();

            var queryParams = BuildQueryString(request);
            var response = await _httpClient.GetAsync(
                $"/customers?{queryParams}",
                cancellationToken);

            await EnsureSuccessAsync(response, cancellationToken);

            return await response.Content.ReadFromJsonAsync<PageResponse<CustomerResponse>>(
                _jsonOptions,
                cancellationToken) ?? throw new InvalidOperationException("Resposta vazia");
        }

        /// <inheritdoc />
        public async IAsyncEnumerable<CustomerResponse> ListAllAsync(
            CustomerListRequest? request = null,
            [EnumeratorCancellation] CancellationToken cancellationToken = default)
        {
            request ??= new CustomerListRequest();
            var hasMore = true;
            var offset = 0;

            while (hasMore && !cancellationToken.IsCancellationRequested)
            {
                request.Offset = offset;
                var page = await ListAsync(request, cancellationToken);

                foreach (var item in page.Data)
                {
                    yield return item;
                }

                hasMore = page.HasMore;
                offset += page.Limit;
            }
        }

        /// <inheritdoc />
        public async Task<CustomerResponse> UpdateAsync(
            string id,
            CustomerRequest request,
            CancellationToken cancellationToken = default)
        {
            var response = await _httpClient.PostAsJsonAsync(
                $"/customers/{id}",
                request,
                _jsonOptions,
                cancellationToken);

            await EnsureSuccessAsync(response, cancellationToken);

            return await response.Content.ReadFromJsonAsync<CustomerResponse>(
                _jsonOptions,
                cancellationToken) ?? throw new InvalidOperationException("Resposta vazia");
        }

        /// <inheritdoc />
        public async Task DeleteAsync(
            string id,
            CancellationToken cancellationToken = default)
        {
            var response = await _httpClient.DeleteAsync(
                $"/customers/{id}",
                cancellationToken);

            await EnsureSuccessAsync(response, cancellationToken);
        }

        /// <inheritdoc />
        public async Task<CustomerResponse> RestoreAsync(
            string id,
            CancellationToken cancellationToken = default)
        {
            var response = await _httpClient.PostAsync(
                $"/customers/{id}/restore",
                null,
                cancellationToken);

            await EnsureSuccessAsync(response, cancellationToken);

            return await response.Content.ReadFromJsonAsync<CustomerResponse>(
                _jsonOptions,
                cancellationToken) ?? throw new InvalidOperationException("Resposta vazia");
        }

        private static string BuildQueryString(CustomerListRequest request)
        {
            var parameters = new List<string>();

            if (request.Offset > 0)
                parameters.Add($"offset={request.Offset}");
            
            if (request.Limit != 10)
                parameters.Add($"limit={request.Limit}");

            if (!string.IsNullOrEmpty(request.Name))
                parameters.Add($"name={Uri.EscapeDataString(request.Name)}");

            if (!string.IsNullOrEmpty(request.Email))
                parameters.Add($"email={Uri.EscapeDataString(request.Email)}");

            if (!string.IsNullOrEmpty(request.CpfCnpj))
                parameters.Add($"cpfCnpj={request.CpfCnpj}");

            if (!string.IsNullOrEmpty(request.GroupName))
                parameters.Add($"groupName={Uri.EscapeDataString(request.GroupName)}");

            if (!string.IsNullOrEmpty(request.ExternalReference))
                parameters.Add($"externalReference={Uri.EscapeDataString(request.ExternalReference)}");

            return string.Join("&", parameters);
        }

        private async Task EnsureSuccessAsync(
            HttpResponseMessage response,
            CancellationToken cancellationToken)
        {
            if (response.IsSuccessStatusCode)
                return;

            var content = await response.Content.ReadAsStringAsync(cancellationToken);

            try
            {
                var errorResponse = JsonSerializer.Deserialize<AsaasErrorResponse>(
                    content,
                    _jsonOptions);

                throw new AsaasApiException(
                    response.StatusCode,
                    "Erro na API do Asaas",
                    errorResponse?.Errors,
                    content);
            }
            catch (JsonException)
            {
                throw new AsaasApiException(
                    response.StatusCode,
                    $"Erro na API do Asaas: {content}",
                    null,
                    content);
            }
        }
    }
}
```

## 🧪 Testes Unitários (Exemplo: CustomersClientTests)

```csharp
using System.Net;
using System.Net.Http.Json;
using Xunit;
using FluentAssertions;
using RichardSzalay.MockHttp;
using ZDZCode.Payments.Asaas.Clients;
using ZDZCode.Payments.Asaas.Clients.Customers.Models;
using ZDZCode.Payments.Asaas.Errors;

namespace ZDZCode.Payments.Asaas.Tests.Clients.Customers
{
    /// <summary>
    /// Testes unitários para o cliente de customers.
    /// </summary>
    public class CustomersClientTests
    {
        private readonly MockHttpMessageHandler _mockHttp;
        private readonly IHttpClientFactory _httpClientFactory;

        /// <summary>
        /// Inicializa uma nova instância dos testes.
        /// </summary>
        public CustomersClientTests()
        {
            _mockHttp = new MockHttpMessageHandler();
            var httpClient = _mockHttp.ToHttpClient();
            httpClient.BaseAddress = new Uri("https://sandbox.asaas.com/api/v3");

            _httpClientFactory = Substitute.For<IHttpClientFactory>();
            _httpClientFactory.CreateClient("Asaas").Returns(httpClient);
        }

        /// <summary>
        /// Testa se o método CreateAsync retorna um cliente quando a requisição é válida.
        /// </summary>
        [Fact]
        public async Task CreateAsync_DeveRetornarCliente_QuandoRequisicaoValida()
        {
            // Arrange
            var request = new CustomerRequest
            {
                Name = "João Silva",
                Email = "joao@example.com",
                CpfCnpj = "12345678901"
            };

            var expectedResponse = new CustomerResponse
            {
                Id = "cus_123456",
                Name = "João Silva",
                Email = "joao@example.com",
                DateCreated = DateTime.Now
            };

            _mockHttp.When(HttpMethod.Post, "https://sandbox.asaas.com/api/v3/customers")
                .Respond("application/json", JsonSerializer.Serialize(expectedResponse));

            var client = new CustomersClient(_httpClientFactory);

            // Act
            var result = await client.CreateAsync(request);

            // Assert
            result.Should().NotBeNull();
            result.Id.Should().Be("cus_123456");
            result.Name.Should().Be("João Silva");
            result.Email.Should().Be("joao@example.com");
        }

        /// <summary>
        /// Testa se o método CreateAsync lança AsaasApiException quando recebe erro 400.
        /// </summary>
        [Fact]
        public async Task CreateAsync_DeveLancarAsaasApiException_QuandoErro400()
        {
            // Arrange
            var request = new CustomerRequest { Name = "" };

            var errorResponse = new AsaasErrorResponse
            {
                Errors = new List<AsaasError>
                {
                    new AsaasError
                    {
                        Code = "invalid_action",
                        Description = "O nome é obrigatório"
                    }
                }
            };

            _mockHttp.When(HttpMethod.Post, "https://sandbox.asaas.com/api/v3/customers")
                .Respond(HttpStatusCode.BadRequest, "application/json", 
                    JsonSerializer.Serialize(errorResponse));

            var client = new CustomersClient(_httpClientFactory);

            // Act
            var act = async () => await client.CreateAsync(request);

            // Assert
            await act.Should().ThrowAsync<AsaasApiException>()
                .Where(ex => ex.StatusCode == HttpStatusCode.BadRequest)
                .Where(ex => ex.Errors!.Any(e => e.Code == "invalid_action"));
        }

        /// <summary>
        /// Testa se o método ListAllAsync itera corretamente por todas as páginas.
        /// </summary>
        [Fact]
        public async Task ListAllAsync_DeveIterarTodasPaginas()
        {
            // Arrange
            var page1 = new PageResponse<CustomerResponse>
            {
                HasMore = true,
                Limit = 2,
                Offset = 0,
                Data = new List<CustomerResponse>
                {
                    new() { Id = "cus_1", Name = "Cliente 1" },
                    new() { Id = "cus_2", Name = "Cliente 2" }
                }
            };

            var page2 = new PageResponse<CustomerResponse>
            {
                HasMore = false,
                Limit = 2,
                Offset = 2,
                Data = new List<CustomerResponse>
                {
                    new() { Id = "cus_3", Name = "Cliente 3" }
                }
            };

            _mockHttp.When("https://sandbox.asaas.com/api/v3/customers?offset=0&limit=10")
                .Respond("application/json", JsonSerializer.Serialize(page1));

            _mockHttp.When("https://sandbox.asaas.com/api/v3/customers?offset=2&limit=10")
                .Respond("application/json", JsonSerializer.Serialize(page2));

            var client = new CustomersClient(_httpClientFactory);

            // Act
            var results = new List<CustomerResponse>();
            await foreach (var customer in client.ListAllAsync())
            {
                results.Add(customer);
            }

            // Assert
            results.Should().HaveCount(3);
            results.Select(c => c.Id).Should().ContainInOrder("cus_1", "cus_2", "cus_3");
        }
    }
}
```

## 📊 Checklist de Implementação

Para cada módulo/endpoint:

- [ ] Criar interface `I{Module}Client` com comentários XML
- [ ] Criar DTOs de Request herdando de `PageRequest` quando aplicável
- [ ] Criar DTOs de Response com todas as propriedades da API
- [ ] Implementar classe `{Module}Client` com todos os métodos
- [ ] Adicionar links de referência da documentação em cada método
- [ ] Implementar testes unitários para:
  - [ ] Cenário de sucesso (200)
  - [ ] Validação de entrada (400)
  - [ ] Não autorizado (401)
  - [ ] Não encontrado (404)
  - [ ] Rate limit (429)
  - [ ] Erro de servidor (500)
  - [ ] Paginação (ListAllAsync)
  - [ ] Cancelamento (CancellationToken)
- [ ] Registrar cliente no `ServiceCollectionExtensions`
- [ ] Atualizar documentação do módulo

## 📚 Referências

- **Documentação Oficial**: https://docs.asaas.com
- **Postman Collection**: https://raw.githubusercontent.com/asaasdev/postman-collection/main/asaasPostmanCollection.json
- **Padrões ZDZCode**: Consultar `AGENTS.md` no repositório zdzcode-guidelines

---

**Nota**: Este guia serve como template para implementar todos os 20 módulos da API do Asaas. Cada módulo deve seguir a mesma estrutura e padrões apresentados aqui.
