# Guia Rápido - ZDZCode.Payments.Asaas

Guia prático para começar a usar a biblioteca de integração com Asaas em 5 minutos.

## 🚀 Início Rápido

### 1. Instalação

```bash
dotnet add package ZDZCode.Payments.Asaas
```

### 2. Configuração

```csharp
// Program.cs ou Startup.cs
using ZDZCode.Payments.Asaas;

builder.Services.AddAsaas(options =>
{
    options.ApiKey = "sua-api-key-aqui"; // OBRIGATÓRIO
    options.UseSandbox = true; // true para testes, false para produção
});
```

### 3. Primeiro Uso

```csharp
using ZDZCode.Payments.Asaas.Clients;

public class PaymentService
{
    private readonly ICustomersClient _customers;
    private readonly IPaymentsClient _payments;

    public PaymentService(ICustomersClient customers, IPaymentsClient payments)
    {
        _customers = customers;
        _payments = payments;
    }

    public async Task<string> CreateFirstPayment()
    {
        // 1. Criar cliente
        var customer = await _customers.CreateAsync(new CustomerRequest
        {
            Name = "João Silva",
            Email = "joao@example.com",
            CpfCnpj = "12345678901"
        });

        // 2. Criar cobrança
        var payment = await _payments.CreateAsync(new PaymentRequest
        {
            Customer = customer.Id,
            BillingType = BillingType.Boleto,
            Value = 100.00m,
            DueDate = DateTime.Now.AddDays(7)
        });

        // 3. Retornar URL de pagamento
        return payment.InvoiceUrl;
    }
}
```

## 📋 Casos de Uso Comuns

### Criar Cobrança com Boleto

```csharp
/// <summary>
/// Cria uma cobrança via boleto bancário.
/// Referência: https://docs.asaas.com/reference/criar-nova-cobranca
/// </summary>
var payment = await _paymentsClient.CreateAsync(new PaymentRequest
{
    Customer = "cus_000000000000",
    BillingType = BillingType.Boleto,
    Value = 150.00m,
    DueDate = DateTime.Now.AddDays(7),
    Description = "Mensalidade Janeiro/2024",
    ExternalReference = "REF-001" // Seu ID interno
});

Console.WriteLine($"URL do boleto: {payment.InvoiceUrl}");
Console.WriteLine($"Linha digitável: {payment.BankSlipIdentificationField}");
```

### Criar Cobrança com Pix

```csharp
/// <summary>
/// Cria uma cobrança via Pix com QR Code.
/// Referência: https://docs.asaas.com/reference/criar-nova-cobranca
/// </summary>
var payment = await _paymentsClient.CreateAsync(new PaymentRequest
{
    Customer = "cus_000000000000",
    BillingType = BillingType.Pix,
    Value = 50.00m,
    DueDate = DateTime.Now.AddDays(1)
});

// Obter QR Code
var qrCode = await _paymentsClient.GetPixQrCodeAsync(payment.Id);

Console.WriteLine($"QR Code Pix (copiar e colar): {qrCode.Payload}");
Console.WriteLine($"URL da imagem: {qrCode.EncodedImage}");
```

### Criar Cobrança com Cartão de Crédito

```csharp
/// <summary>
/// Processa pagamento com cartão de crédito.
/// IMPORTANTE: Use HTTPS e nunca armazene dados do cartão.
/// Referência: https://docs.asaas.com/reference/criar-cobranca-com-cartao-de-credito
/// </summary>
var payment = await _paymentsClient.CreateWithCreditCardAsync(new PaymentCreditCardRequest
{
    Customer = "cus_000000000000",
    BillingType = BillingType.CreditCard,
    Value = 99.90m,
    DueDate = DateTime.Now,
    CreditCard = new CreditCardInfo
    {
        HolderName = "JOAO SILVA",
        Number = "5162306219378829",
        ExpiryMonth = "12",
        ExpiryYear = "2025",
        Ccv = "123"
    },
    CreditCardHolderInfo = new CreditCardHolderInfo
    {
        Name = "João Silva",
        Email = "joao@example.com",
        CpfCnpj = "12345678901",
        PostalCode = "01310-100",
        AddressNumber = "100",
        Phone = "11987654321"
    }
});

if (payment.Status == PaymentStatus.Confirmed)
{
    Console.WriteLine("✅ Pagamento aprovado!");
}
else
{
    Console.WriteLine($"❌ Pagamento recusado: {payment.CreditCardMessage}");
}
```

### Criar Assinatura Recorrente

```csharp
/// <summary>
/// Cria uma assinatura para cobranças recorrentes mensais.
/// Referência: https://docs.asaas.com/reference/criar-nova-assinatura
/// </summary>
var subscription = await _subscriptionsClient.CreateAsync(new SubscriptionRequest
{
    Customer = "cus_000000000000",
    BillingType = BillingType.CreditCard,
    Value = 49.90m,
    Cycle = SubscriptionCycle.Monthly,
    NextDueDate = DateTime.Now.AddMonths(1),
    Description = "Plano Mensal Premium"
});

Console.WriteLine($"Assinatura criada: {subscription.Id}");
Console.WriteLine($"Próximo vencimento: {subscription.NextDueDate:dd/MM/yyyy}");
```

### Listar Cobranças com Filtros

```csharp
/// <summary>
/// Lista cobranças com filtros e paginação.
/// Referência: https://docs.asaas.com/reference/listar-cobrancas
/// </summary>
var payments = await _paymentsClient.ListAsync(new PaymentListRequest
{
    Customer = "cus_000000000000",
    Status = PaymentStatus.Pending,
    DueDateGe = DateTime.Now,
    DueDateLe = DateTime.Now.AddMonths(1),
    Limit = 20
});

Console.WriteLine($"Cobranças pendentes: {payments.TotalCount}");
foreach (var payment in payments.Data)
{
    Console.WriteLine($"- R$ {payment.Value:N2} - Vence em {payment.DueDate:dd/MM/yyyy}");
}
```

### Iterar Sobre Todas as Páginas

```csharp
/// <summary>
/// Itera automaticamente sobre todas as páginas de resultados.
/// </summary>
await foreach (var customer in _customersClient.ListAllAsync())
{
    Console.WriteLine($"Processando cliente: {customer.Name}");
    // Seu código aqui
}
```

### Configurar Webhook

```csharp
/// <summary>
/// Configura webhook para receber notificações de pagamentos.
/// Referência: https://docs.asaas.com/reference/criar-ou-atualizar-configuracoes-para-webhook
/// </summary>
var webhook = await _webhooksClient.CreateOrUpdateAsync(new WebhookRequest
{
    Url = "https://seusite.com/webhooks/asaas/payments",
    Email = "notificacoes@seusite.com",
    Enabled = true,
    ApiVersion = 3,
    AuthToken = "seu-token-secreto"
});

Console.WriteLine($"✅ Webhook configurado: {webhook.Url}");
```

### Receber Notificação de Webhook

```csharp
/// <summary>
/// Endpoint ASP.NET Core para receber webhooks do Asaas.
/// </summary>
[ApiController]
[Route("webhooks/asaas")]
public class AsaasWebhookController : ControllerBase
{
    private readonly IPaymentsClient _paymentsClient;
    private readonly ILogger<AsaasWebhookController> _logger;

    public AsaasWebhookController(
        IPaymentsClient paymentsClient,
        ILogger<AsaasWebhookController> logger)
    {
        _paymentsClient = paymentsClient;
        _logger = logger;
    }

    /// <summary>
    /// Recebe notificações de eventos de pagamento.
    /// Documentação: https://docs.asaas.com/docs/webhooks-1
    /// </summary>
    [HttpPost("payments")]
    public async Task<IActionResult> ReceivePaymentWebhook(
        [FromBody] AsaasWebhookPayload payload,
        [FromHeader(Name = "asaas-access-token")] string accessToken)
    {
        try
        {
            // 1. Validar token (implemente sua validação)
            if (!IsValidToken(accessToken))
            {
                _logger.LogWarning("Token inválido recebido");
                return Unauthorized();
            }

            // 2. Processar evento
            switch (payload.Event)
            {
                case "PAYMENT_CONFIRMED":
                    _logger.LogInformation($"Pagamento confirmado: {payload.Payment.Id}");
                    await ProcessPaymentConfirmed(payload.Payment);
                    break;

                case "PAYMENT_RECEIVED":
                    _logger.LogInformation($"Pagamento recebido: {payload.Payment.Id}");
                    await ProcessPaymentReceived(payload.Payment);
                    break;

                case "PAYMENT_OVERDUE":
                    _logger.LogWarning($"Pagamento vencido: {payload.Payment.Id}");
                    await ProcessPaymentOverdue(payload.Payment);
                    break;

                default:
                    _logger.LogInformation($"Evento não tratado: {payload.Event}");
                    break;
            }

            // 3. Sempre retornar 200 para confirmar recebimento
            return Ok();
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Erro ao processar webhook");
            // Retornar 200 mesmo com erro para evitar reenvios desnecessários
            return Ok();
        }
    }

    private bool IsValidToken(string token)
    {
        // Implemente sua validação de token
        return !string.IsNullOrEmpty(token);
    }

    private async Task ProcessPaymentConfirmed(PaymentWebhookData payment)
    {
        // Buscar informações completas do pagamento
        var fullPayment = await _paymentsClient.GetByIdAsync(payment.Id);
        
        // Atualizar seu sistema
        // Enviar e-mail de confirmação
        // Liberar acesso/produto
        
        _logger.LogInformation($"Pagamento {payment.Id} processado com sucesso");
    }

    private Task ProcessPaymentReceived(PaymentWebhookData payment)
    {
        // Processar pagamento recebido
        return Task.CompletedTask;
    }

    private Task ProcessPaymentOverdue(PaymentWebhookData payment)
    {
        // Processar pagamento vencido
        // Enviar lembrete ao cliente
        return Task.CompletedTask;
    }
}

public class AsaasWebhookPayload
{
    /// <summary>
    /// Tipo do evento (ex: PAYMENT_CONFIRMED, PAYMENT_RECEIVED, etc.)
    /// </summary>
    public string Event { get; set; } = string.Empty;

    /// <summary>
    /// Dados do pagamento relacionado ao evento
    /// </summary>
    public PaymentWebhookData Payment { get; set; } = new();
}

public class PaymentWebhookData
{
    public string Id { get; set; } = string.Empty;
    public decimal Value { get; set; }
    public string Status { get; set; } = string.Empty;
    public DateTime DueDate { get; set; }
    public string Customer { get; set; } = string.Empty;
}
```

## ⚠️ Tratamento de Erros

### Tratamento Básico

```csharp
try
{
    var payment = await _paymentsClient.CreateAsync(request);
    Console.WriteLine($"✅ Cobrança criada: {payment.Id}");
}
catch (AsaasApiException ex)
{
    Console.WriteLine($"❌ Erro na API: {ex.Message}");
    Console.WriteLine($"Status Code: {ex.StatusCode}");
    
    if (ex.Errors != null)
    {
        foreach (var error in ex.Errors)
        {
            Console.WriteLine($"  - [{error.Code}] {error.Description}");
        }
    }
}
catch (HttpRequestException ex)
{
    Console.WriteLine($"❌ Erro de rede: {ex.Message}");
}
catch (TaskCanceledException)
{
    Console.WriteLine($"❌ Timeout: A requisição demorou muito");
}
```

### Tratamento por Código de Erro

```csharp
try
{
    var customer = await _customersClient.CreateAsync(request);
}
catch (AsaasApiException ex)
{
    var errorCode = ex.Errors?.FirstOrDefault()?.Code;
    
    switch (errorCode)
    {
        case "customer_cpfCnpj_already_in_use":
            Console.WriteLine("Este CPF/CNPJ já está cadastrado");
            break;
            
        case "invalid_email":
            Console.WriteLine("E-mail inválido");
            break;
            
        case "invalid_action":
            Console.WriteLine("Ação inválida - verifique os dados");
            break;
            
        default:
            Console.WriteLine($"Erro desconhecido: {ex.Message}");
            break;
    }
}
```

### Retry Manual

```csharp
public async Task<PaymentResponse?> CreatePaymentWithRetry(
    PaymentRequest request,
    int maxAttempts = 3)
{
    for (int attempt = 1; attempt <= maxAttempts; attempt++)
    {
        try
        {
            return await _paymentsClient.CreateAsync(request);
        }
        catch (AsaasApiException ex) when (ex.StatusCode == System.Net.HttpStatusCode.TooManyRequests)
        {
            if (attempt == maxAttempts)
                throw;
            
            // Esperar antes de tentar novamente (rate limit)
            var delay = TimeSpan.FromSeconds(Math.Pow(2, attempt));
            Console.WriteLine($"Rate limit atingido. Tentando novamente em {delay.TotalSeconds}s...");
            await Task.Delay(delay);
        }
        catch (HttpRequestException) when (attempt < maxAttempts)
        {
            // Erro de rede - tentar novamente
            Console.WriteLine($"Erro de rede. Tentativa {attempt}/{maxAttempts}");
            await Task.Delay(TimeSpan.FromSeconds(2));
        }
    }
    
    return null;
}
```

## 🔧 Configuração Avançada

### Configuração Completa

```csharp
builder.Services.AddAsaas(options =>
{
    // Obrigatório
    options.ApiKey = Configuration["Asaas:ApiKey"];
    
    // Ambiente
    options.UseSandbox = Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT") != "Production";
    
    // Timeouts e Retry
    options.Timeout = TimeSpan.FromSeconds(30);
    options.EnableRetry = true;
    options.MaxRetryAttempts = 3;
    
    // Circuit Breaker (opcional)
    options.EnableCircuitBreaker = true;
    options.CircuitBreakerThreshold = 5;
    options.CircuitBreakerDuration = TimeSpan.FromMinutes(1);
    
    // User Agent personalizado
    options.UserAgent = "MeuSistema/1.0";
});
```

### Usando appsettings.json

```json
{
  "Asaas": {
    "ApiKey": "sua-api-key-aqui",
    "UseSandbox": true,
    "Timeout": "00:00:30",
    "EnableRetry": true,
    "MaxRetryAttempts": 3,
    "EnableCircuitBreaker": false
  }
}
```

```csharp
builder.Services.AddAsaas(builder.Configuration.GetSection("Asaas"));
```

### Usando Variáveis de Ambiente

```bash
export ASAAS__APIKEY="sua-api-key-aqui"
export ASAAS__USESANDBOX="true"
```

```csharp
builder.Services.AddAsaas(options =>
{
    options.ApiKey = Configuration["Asaas:ApiKey"];
    options.UseSandbox = Configuration.GetValue<bool>("Asaas:UseSandbox");
});
```

## 📊 Consultas Financeiras

### Consultar Saldo

```csharp
/// <summary>
/// Consulta o saldo disponível na conta.
/// Referência: https://docs.asaas.com/reference/recuperar-saldo-da-conta
/// </summary>
var balance = await _financeClient.GetBalanceAsync();

Console.WriteLine($"Saldo disponível: R$ {balance.Available:N2}");
Console.WriteLine($"Saldo bloqueado: R$ {balance.Blocked:N2}");
Console.WriteLine($"Total: R$ {(balance.Available + balance.Blocked):N2}");
```

### Consultar Extrato

```csharp
/// <summary>
/// Recupera o extrato de movimentações financeiras.
/// Referência: https://docs.asaas.com/reference/recuperar-extrato
/// </summary>
var transactions = await _financialTransactionsClient.ListAsync(new FinancialTransactionListRequest
{
    StartDate = DateTime.Now.AddMonths(-1),
    EndDate = DateTime.Now,
    Limit = 100
});

decimal total = 0;
foreach (var transaction in transactions.Data)
{
    var sinal = transaction.Value >= 0 ? "+" : "";
    Console.WriteLine($"{transaction.Date:dd/MM/yyyy} - {transaction.Description}: {sinal}R$ {transaction.Value:N2}");
    total += transaction.Value;
}

Console.WriteLine($"\nMovimentação total: R$ {total:N2}");
```

## 🎯 Dicas e Boas Práticas

### 1. Sempre Use Sandbox para Testes

```csharp
// ❌ Não fazer em desenvolvimento
options.UseSandbox = false;

// ✅ Usar sandbox para testes
options.UseSandbox = true;

// ✅ Baseado no ambiente
options.UseSandbox = !Environment.IsProduction();
```

### 2. Valide Dados Antes de Enviar

```csharp
// ✅ Validar CPF/CNPJ
if (!CpfCnpjValidator.IsValid(cpfCnpj))
{
    throw new ValidationException("CPF/CNPJ inválido");
}

// ✅ Validar e-mail
if (!EmailValidator.IsValid(email))
{
    throw new ValidationException("E-mail inválido");
}

// ✅ Validar valor mínimo
if (value < 3.00m)
{
    throw new ValidationException("Valor mínimo é R$ 3,00");
}
```

### 3. Use IDs Externos para Rastreamento

```csharp
// ✅ Usar referência externa
var payment = await _paymentsClient.CreateAsync(new PaymentRequest
{
    // ... outros campos
    ExternalReference = $"PEDIDO-{pedidoId}" // Seu ID interno
});

// Depois você pode buscar por esta referência
var payments = await _paymentsClient.ListAsync(new PaymentListRequest
{
    ExternalReference = $"PEDIDO-{pedidoId}"
});
```

### 4. Implemente Idempotência

```csharp
public async Task<PaymentResponse> CreatePaymentIdempotent(PaymentRequest request)
{
    // Verificar se já existe cobrança para este pedido
    var existing = await _paymentsClient.ListAsync(new PaymentListRequest
    {
        ExternalReference = request.ExternalReference
    });
    
    if (existing.Data.Any())
    {
        return existing.Data.First(); // Retornar cobrança existente
    }
    
    // Criar nova cobrança
    return await _paymentsClient.CreateAsync(request);
}
```

### 5. Use CancellationToken

```csharp
// ✅ Permitir cancelamento de operações longas
public async Task ProcessPayments(CancellationToken cancellationToken)
{
    await foreach (var payment in _paymentsClient.ListAllAsync(cancellationToken: cancellationToken))
    {
        if (cancellationToken.IsCancellationRequested)
        {
            break;
        }
        
        await ProcessPayment(payment);
    }
}
```

## 📚 Links Úteis

- **Documentação Completa**: [README.md](asaas-library-readme.md)
- **Referência de Endpoints**: [Endpoints Reference](asaas-endpoints-reference.md)
- **Guia de Implementação**: [Implementation Guide](asaas-implementation-guide.md)
- **Exemplos de Código**: [Code Examples](asaas-code-examples.md)
- **Documentação Oficial Asaas**: https://docs.asaas.com
- **Collection Postman**: https://raw.githubusercontent.com/asaasdev/postman-collection/main/asaasPostmanCollection.json

## ❓ Perguntas Frequentes

### Como obter uma API Key?

1. Acesse https://www.asaas.com
2. Faça login na sua conta
3. Vá em **Integrações** > **API Key**
4. Gere uma nova chave ou use uma existente

### Qual a diferença entre Sandbox e Produção?

- **Sandbox**: Ambiente de testes, não processa pagamentos reais
- **Produção**: Ambiente real, processa pagamentos verdadeiros

### Como testar sem cartão de crédito real?

Use os cartões de teste do Asaas:
- **Aprovado**: 5162306219378829
- **Recusado**: 5105105105105100

### Qual o limite de requisições?

O Asaas implementa rate limiting. A biblioteca já possui retry automático para lidar com isso.

### Como sei se um pagamento foi confirmado?

Configure webhooks ou consulte o status:
```csharp
var payment = await _paymentsClient.GetByIdAsync(paymentId);
if (payment.Status == PaymentStatus.Confirmed)
{
    // Pagamento confirmado!
}
```

---

**Pronto para começar?** Escolha um exemplo acima e adapte para seu caso de uso! 🚀
