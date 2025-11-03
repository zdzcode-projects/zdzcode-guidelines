# ZDZCode.Payments.Asaas

Biblioteca C# para integração completa com a API de pagamentos do Asaas, seguindo os padrões e arquitetura da ZDZCode.

[![.NET](https://img.shields.io/badge/.NET-8.0-512BD4)](https://dotnet.microsoft.com/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![NuGet](https://img.shields.io/badge/NuGet-Package-blue)](https://www.nuget.org/)

## 📋 Índice

- [Sobre o Projeto](#sobre-o-projeto)
- [Recursos](#recursos)
- [Instalação](#instalação)
- [Configuração](#configuração)
- [Autenticação](#autenticação)
- [Uso Básico](#uso-básico)
- [Módulos e Endpoints](#módulos-e-endpoints)
- [Paginação](#paginação)
- [Tratamento de Erros](#tratamento-de-erros)
- [Resiliência](#resiliência)
- [Testes](#testes)
- [Exemplos](#exemplos)
- [Documentação da API Asaas](#documentação-da-api-asaas)
- [Contribuindo](#contribuindo)

## 🎯 Sobre o Projeto

Esta biblioteca fornece uma interface idiomática em C# para interagir com a API do Asaas, uma plataforma completa de pagamentos e gestão financeira. A implementação segue os padrões de arquitetura da ZDZCode, incluindo:

- **Cobertura completa**: 118 endpoints implementados
- **Tipos fortes**: Modelos tipados para todas as requisições e respostas
- **Resiliência**: Retry policies, timeouts e circuit breaker
- **Dependency Injection**: Integração nativa com Microsoft.Extensions.DependencyInjection
- **Testes**: Cobertura de testes >= 90%

## ✨ Recursos

- ✅ **118 endpoints** mapeados da API oficial do Asaas
- 🔐 **Autenticação**: Suporte completo via API Key
- 🔄 **Paginação**: Iteradores assíncronos para listar grandes conjuntos de dados
- 🛡️ **Resiliência**: Políticas de retry com backoff exponencial
- 📊 **Telemetria**: Logging estruturado sem expor dados sensíveis
- 🧪 **Testabilidade**: Interfaces e mocks para facilitar testes
- 📦 **NuGet**: Pronto para distribuição via NuGet

## 📦 Instalação

```bash
dotnet add package ZDZCode.Payments.Asaas
```

Ou via Package Manager Console:

```powershell
Install-Package ZDZCode.Payments.Asaas
```

## ⚙️ Configuração

### Configuração Básica

```csharp
using Microsoft.Extensions.DependencyInjection;
using ZDZCode.Payments.Asaas;

var services = new ServiceCollection();

services.AddAsaas(options =>
{
    options.ApiKey = "sua-api-key-aqui";
    options.UseSandbox = true; // true para ambiente de testes
    options.Timeout = TimeSpan.FromSeconds(30);
});

var serviceProvider = services.BuildServiceProvider();
```

### Configuração via appsettings.json

```json
{
  "Asaas": {
    "ApiKey": "sua-api-key-aqui",
    "UseSandbox": true,
    "BaseUrl": "https://sandbox.asaas.com/api/v3",
    "Timeout": "00:00:30",
    "EnableRetry": true,
    "MaxRetryAttempts": 3
  }
}
```

```csharp
services.AddAsaas(Configuration.GetSection("Asaas"));
```

### Opções Disponíveis

| Propriedade | Tipo | Padrão | Descrição |
|------------|------|--------|-----------|
| `ApiKey` | `string` | - | **Obrigatório**. Chave de API do Asaas |
| `UseSandbox` | `bool` | `false` | Usar ambiente sandbox |
| `BaseUrl` | `string` | `https://api.asaas.com/v3` | URL base da API |
| `Timeout` | `TimeSpan` | `30s` | Timeout para requisições |
| `EnableRetry` | `bool` | `true` | Habilitar retry automático |
| `MaxRetryAttempts` | `int` | `3` | Número máximo de tentativas |
| `UserAgent` | `string` | `ZDZCode.Payments.Asaas/1.0` | User agent customizado |

## 🔐 Autenticação

A API do Asaas utiliza autenticação via **API Key** enviada no header `access_token` de todas as requisições.

### Obtendo sua API Key

1. Acesse o [painel do Asaas](https://www.asaas.com/)
2. Navegue até **Integrações** > **API Key**
3. Gere uma nova chave ou utilize uma existente
4. Configure no ambiente:

```bash
# Variável de ambiente
export ASAAS_API_KEY="your-api-key-here"
```

### Ambientes

- **Produção**: `https://api.asaas.com/v3`
- **Sandbox**: `https://sandbox.asaas.com/api/v3`

**Importante**: Sempre use o ambiente sandbox para testes e desenvolvimento.

## 🚀 Uso Básico

### Exemplo Completo

```csharp
using ZDZCode.Payments.Asaas;
using ZDZCode.Payments.Asaas.Clients;
using ZDZCode.Payments.Asaas.Models;

// Injeção via DI
public class PaymentService
{
    private readonly ICustomersClient _customersClient;
    private readonly IPaymentsClient _paymentsClient;

    /// <summary>
    /// Inicializa uma nova instância do serviço de pagamentos.
    /// </summary>
    public PaymentService(
        ICustomersClient customersClient,
        IPaymentsClient paymentsClient)
    {
        _customersClient = customersClient;
        _paymentsClient = paymentsClient;
    }

    /// <summary>
    /// Cria um cliente e uma cobrança associada.
    /// </summary>
    /// <returns>ID da cobrança criada</returns>
    public async Task<string> CreateCustomerAndPayment()
    {
        try
        {
            // Criar cliente
            var customerRequest = new CustomerRequest
            {
                Name = "João Silva",
                Email = "joao.silva@example.com",
                CpfCnpj = "12345678901",
                Phone = "11987654321"
            };

            var customer = await _customersClient.CreateAsync(customerRequest);

            // Criar cobrança
            var paymentRequest = new PaymentRequest
            {
                Customer = customer.Id,
                BillingType = BillingType.Boleto,
                Value = 100.00m,
                DueDate = DateTime.Now.AddDays(7)
            };

            var payment = await _paymentsClient.CreateAsync(paymentRequest);

            return payment.Id;
        }
        catch (AsaasApiException ex)
        {
            Console.WriteLine($"Erro na API: {ex.Message}");
            Console.WriteLine($"Status Code: {ex.StatusCode}");
            throw;
        }
    }
}
```

## 📚 Módulos e Endpoints

A biblioteca está organizada em **20 módulos** que cobrem **118 endpoints** da API do Asaas.

### 1. Clientes (`ICustomersClient`)

Gerenciamento de clientes (pessoas ou empresas).

**Documentação**: [Clientes - Asaas Docs](https://docs.asaas.com/reference/clientes)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `CreateAsync` | `POST /v3/customers` | Criar novo cliente |
| `GetByIdAsync` | `GET /v3/customers/{id}` | Recuperar cliente específico |
| `ListAsync` | `GET /v3/customers` | Listar clientes com filtros |
| `UpdateAsync` | `POST /v3/customers/{id}` | Atualizar dados do cliente |
| `DeleteAsync` | `DELETE /v3/customers/{id}` | Remover cliente |
| `RestoreAsync` | `POST /v3/customers/{id}/restore` | Restaurar cliente removido |

**Exemplo de uso**:
```csharp
/// <summary>
/// Cria um novo cliente no Asaas.
/// </summary>
/// <param name="request">Dados do cliente a ser criado</param>
/// <returns>Cliente criado com ID gerado</returns>
/// <see href="https://docs.asaas.com/reference/criar-novo-cliente">Documentação Oficial</see>
var customer = await _customersClient.CreateAsync(new CustomerRequest
{
    Name = "João Silva",
    Email = "joao@example.com",
    CpfCnpj = "12345678901"
});
```

### 2. Cobranças (`IPaymentsClient`)

Gerenciamento de cobranças e recebimentos.

**Documentação**: [Cobranças - Asaas Docs](https://docs.asaas.com/reference/cobrancas)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `CreateAsync` | `POST /v3/payments` | Criar nova cobrança |
| `CreateWithCreditCardAsync` | `POST /v3/payments` | Criar cobrança com cartão de crédito |
| `CreateInstallmentAsync` | `POST /v3/payments` | Criar cobrança parcelada |
| `CreateWithSplitAsync` | `POST /v3/payments` | Criar cobrança com split de pagamento |
| `GetByIdAsync` | `GET /v3/payments/{id}` | Recuperar cobrança específica |
| `ListAsync` | `GET /v3/payments` | Listar cobranças |
| `UpdateAsync` | `POST /v3/payments/{id}` | Atualizar cobrança |
| `DeleteAsync` | `DELETE /v3/payments/{id}` | Remover cobrança |
| `RestoreAsync` | `POST /v3/payments/{id}/restore` | Restaurar cobrança removida |
| `RefundAsync` | `POST /v3/payments/{id}/refund` | Estornar cobrança |
| `GetStatusAsync` | `GET /v3/payments/{id}/status` | Obter status da cobrança |
| `GetPixQrCodeAsync` | `GET /v3/payments/{id}/pixQrCode` | Obter QR Code Pix |
| `GetBarcodeAsync` | `GET /v3/payments/{id}/identificationField` | Obter linha digitável do boleto |
| `ConfirmCashReceiptAsync` | `POST /v3/payments/{id}/receiveInCash` | Confirmar recebimento em dinheiro |
| `UndoCashReceiptAsync` | `POST /v3/payments/{id}/undoReceivedInCash` | Desfazer recebimento em dinheiro |

**Exemplo de uso**:
```csharp
/// <summary>
/// Cria uma nova cobrança no Asaas.
/// </summary>
/// <param name="request">Dados da cobrança</param>
/// <returns>Cobrança criada</returns>
/// <see href="https://docs.asaas.com/reference/criar-nova-cobranca">Documentação Oficial</see>
var payment = await _paymentsClient.CreateAsync(new PaymentRequest
{
    Customer = customerId,
    BillingType = BillingType.Boleto,
    Value = 150.00m,
    DueDate = DateTime.Now.AddDays(7),
    Description = "Mensalidade referente a Janeiro/2024"
});
```

### 3. Assinaturas (`ISubscriptionsClient`)

Gerenciamento de cobranças recorrentes.

**Documentação**: [Assinaturas - Asaas Docs](https://docs.asaas.com/reference/assinaturas)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `CreateAsync` | `POST /v3/subscriptions` | Criar nova assinatura |
| `GetByIdAsync` | `GET /v3/subscriptions/{id}` | Recuperar assinatura específica |
| `ListAsync` | `GET /v3/subscriptions` | Listar assinaturas |
| `ListPaymentsAsync` | `GET /v3/subscriptions/{id}/payments` | Listar cobranças da assinatura |
| `GetPaymentBookAsync` | `GET /v3/subscriptions/{id}/paymentBook` | Obter carnê de pagamento |
| `UpdateAsync` | `POST /v3/subscriptions/{id}` | Atualizar assinatura |
| `DeleteAsync` | `DELETE /v3/subscriptions/{id}` | Remover assinatura |
| `ListInvoicesAsync` | `GET /v3/subscriptions/{id}/invoices` | Listar notas fiscais |
| `GetInvoiceSettingsAsync` | `GET /v3/subscriptions/{id}/invoiceSettings` | Obter configurações de NF |
| `GetPaymentBookPdfAsync` | `GET /v3/subscriptions/{id}/paymentBook/pdf` | Baixar carnê em PDF |
| `SendPaymentBookAsync` | `POST /v3/subscriptions/{id}/paymentBook/sendByEmail` | Enviar carnê por e-mail |
| `SendPaymentBookBySmsAsync` | `POST /v3/subscriptions/{id}/paymentBook/sendBySMS` | Enviar carnê por SMS |
| `SendPaymentBookByPostalServiceAsync` | `POST /v3/subscriptions/{id}/paymentBook/sendByPostalService` | Enviar carnê por correios |

**Exemplo de uso**:
```csharp
/// <summary>
/// Cria uma nova assinatura recorrente.
/// </summary>
/// <param name="request">Dados da assinatura</param>
/// <returns>Assinatura criada</returns>
/// <see href="https://docs.asaas.com/reference/criar-nova-assinatura">Documentação Oficial</see>
var subscription = await _subscriptionsClient.CreateAsync(new SubscriptionRequest
{
    Customer = customerId,
    BillingType = BillingType.CreditCard,
    Value = 99.90m,
    Cycle = SubscriptionCycle.Monthly,
    NextDueDate = DateTime.Now.AddMonths(1)
});
```

### 4. Antecipações (`IAnticipationsClient`)

Antecipação de recebíveis.

**Documentação**: [Antecipações - Asaas Docs](https://docs.asaas.com/reference/antecipacoes)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `RequestAsync` | `POST /v3/anticipations` | Solicitar antecipação |
| `SimulateAsync` | `POST /v3/anticipations/simulate` | Simular antecipação |
| `GetByIdAsync` | `GET /v3/anticipations/{id}` | Recuperar antecipação específica |
| `ListAsync` | `GET /v3/anticipations` | Listar antecipações |

**Exemplo de uso**:
```csharp
/// <summary>
/// Simula uma antecipação de recebíveis para verificar taxas e valores.
/// </summary>
/// <param name="request">Parâmetros da simulação</param>
/// <returns>Resultado da simulação com taxas e valores</returns>
/// <see href="https://docs.asaas.com/reference/simular-antecipacao">Documentação Oficial</see>
var simulation = await _anticipationsClient.SimulateAsync(new AnticipationRequest
{
    Payment = paymentId,
    BillingType = BillingType.Boleto
});

Console.WriteLine($"Valor líquido: R$ {simulation.NetValue}");
Console.WriteLine($"Taxa: R$ {simulation.Fee}");
```

### 5. Parcelamentos (`IInstallmentsClient`)

Gerenciamento de parcelamentos.

**Documentação**: [Parcelamentos - Asaas Docs](https://docs.asaas.com/reference/parcelamentos)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `GetByIdAsync` | `GET /v3/installments/{id}` | Recuperar parcelamento específico |
| `ListAsync` | `GET /v3/installments` | Listar parcelamentos |
| `ListPaymentsAsync` | `GET /v3/installments/{id}/payments` | Listar cobranças do parcelamento |
| `GetPaymentBookAsync` | `GET /v3/installments/{id}/paymentBook` | Obter carnê de pagamento |
| `DeleteAsync` | `DELETE /v3/installments/{id}` | Remover parcelamento |
| `RefundAsync` | `POST /v3/installments/{id}/refund` | Estornar parcelamento |

### 6. Negativações (`IPaymentDunningsClient`)

Gerenciamento de negativações e cobranças jurídicas.

**Documentação**: [Negativações - Asaas Docs](https://docs.asaas.com/reference/negativacoes)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `CreateAsync` | `POST /v3/paymentDunnings` | Criar negativação |
| `SimulateAsync` | `POST /v3/paymentDunnings/simulate` | Simular negativação |
| `GetByIdAsync` | `GET /v3/paymentDunnings/{id}` | Recuperar negativação específica |
| `ListAsync` | `GET /v3/paymentDunnings` | Listar negativações |
| `GetHistoryAsync` | `GET /v3/paymentDunnings/{id}/history` | Listar histórico de eventos |
| `GetPartialPaymentsAsync` | `GET /v3/paymentDunnings/{id}/partialPayments` | Listar pagamentos parciais |
| `GetDocumentsAsync` | `GET /v3/paymentDunnings/{id}/documents` | Listar documentos |
| `CancelAsync` | `POST /v3/paymentDunnings/{id}/cancel` | Cancelar negativação |
| `ReplicateAsync` | `POST /v3/paymentDunnings/{id}/replicate` | Replicar negativação |

**Exemplo de uso**:
```csharp
/// <summary>
/// Cria uma negativação para cobrança não paga.
/// </summary>
/// <param name="request">Dados da negativação</param>
/// <returns>Negativação criada</returns>
/// <see href="https://docs.asaas.com/reference/criar-uma-negativacao">Documentação Oficial</see>
var dunning = await _paymentDunningsClient.CreateAsync(new PaymentDunningRequest
{
    Payment = paymentId,
    Type = DunningType.Serasa
});
```

### 7. Notas Fiscais (`IInvoicesClient`)

Emissão e gerenciamento de notas fiscais.

**Documentação**: [Notas Fiscais - Asaas Docs](https://docs.asaas.com/reference/notas-fiscais)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `ScheduleAsync` | `POST /v3/invoices` | Agendar nota fiscal |
| `UpdateAsync` | `PUT /v3/invoices/{id}` | Atualizar nota fiscal |
| `GetByIdAsync` | `GET /v3/invoices/{id}` | Recuperar nota fiscal específica |
| `ListAsync` | `GET /v3/invoices` | Listar notas fiscais |
| `AuthorizeAsync` | `POST /v3/invoices/{id}/authorize` | Emitir nota fiscal |
| `CancelAsync` | `POST /v3/invoices/{id}/cancel` | Cancelar nota fiscal |
| `DownloadPdfAsync` | `GET /v3/invoices/{id}/pdf` | Baixar PDF da nota fiscal |

**Exemplo de uso**:
```csharp
/// <summary>
/// Agenda uma nota fiscal para emissão automática após confirmação do pagamento.
/// </summary>
/// <param name="request">Dados da nota fiscal</param>
/// <returns>Nota fiscal agendada</returns>
/// <see href="https://docs.asaas.com/reference/agendar-nota-fiscal">Documentação Oficial</see>
var invoice = await _invoicesClient.ScheduleAsync(new InvoiceRequest
{
    Payment = paymentId,
    ServiceDescription = "Consultoria em TI",
    Observations = "Serviço prestado em Janeiro/2024"
});
```

### 8. Pix (`IPixClient`)

Operações com Pix.

**Documentação**: [Pix - Asaas Docs](https://docs.asaas.com/reference/pix)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `GetTransactionByIdAsync` | `GET /v3/pix/transactions/{id}` | Recuperar transação específica |
| `ListTransactionsAsync` | `GET /v3/pix/transactions` | Listar transações |
| `CancelScheduledTransactionAsync` | `POST /v3/pix/transactions/{id}/cancel` | Cancelar transação agendada |
| `CreateStaticQrCodeAsync` | `POST /v3/pix/qrCodes/static` | Criar QR Code estático |
| `DecodeQrCodeAsync` | `POST /v3/pix/qrCodes/decode` | Decodificar QR Code |
| `GetQrCodeImageAsync` | `GET /v3/pix/qrCodes/{id}/image` | Obter imagem do QR Code |
| `ListQrCodesAsync` | `GET /v3/pix/qrCodes` | Listar QR Codes |
| `DeleteQrCodeAsync` | `DELETE /v3/pix/qrCodes/{id}` | Remover QR Code |
| `ListAddressKeysAsync` | `GET /v3/pix/addressKeys` | Listar chaves Pix |
| `DeleteAddressKeyAsync` | `DELETE /v3/pix/addressKeys/{id}` | Remover chave Pix |

**Exemplo de uso**:
```csharp
/// <summary>
/// Cria um QR Code Pix estático para recebimento de pagamentos.
/// </summary>
/// <param name="request">Configurações do QR Code</param>
/// <returns>QR Code criado com payload e ID</returns>
/// <see href="https://docs.asaas.com/reference/criar-qrcode-estatico">Documentação Oficial</see>
var qrCode = await _pixClient.CreateStaticQrCodeAsync(new PixQrCodeRequest
{
    AddressKey = "suachave@pix.com.br",
    Description = "Pagamento de serviços",
    Value = 50.00m
});

Console.WriteLine($"QR Code Payload: {qrCode.Payload}");
```

### 9. Transferências (`ITransfersClient`)

Transferências bancárias e para outras contas.

**Documentação**: [Transferências - Asaas Docs](https://docs.asaas.com/reference/transferencias)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `TransferViaTedAsync` | `POST /v3/transfers` | Transferir via TED |
| `TransferViaPixManualAsync` | `POST /v3/transfers` | Transferir via Pix manual |
| `TransferViaPixKeyAsync` | `POST /v3/transfers` | Transferir via chave Pix |
| `TransferViaPixScheduledAsync` | `POST /v3/transfers` | Transferir via Pix agendado |
| `TransferToAsaasAccountAsync` | `POST /v3/transfers` | Transferir para conta Asaas |
| `GetByIdAsync` | `GET /v3/transfers/{id}` | Recuperar transferência |
| `CancelAsync` | `POST /v3/transfers/{id}/cancel` | Cancelar transferência agendada |

**Exemplo de uso**:
```csharp
/// <summary>
/// Realiza uma transferência via Pix usando uma chave.
/// </summary>
/// <param name="request">Dados da transferência</param>
/// <returns>Transferência criada</returns>
/// <see href="https://docs.asaas.com/reference/transferir-via-pix-com-chave">Documentação Oficial</see>
var transfer = await _transfersClient.TransferViaPixKeyAsync(new TransferRequest
{
    Value = 100.00m,
    PixAddressKey = "destinatario@email.com",
    Description = "Pagamento de fornecedor"
});
```

### 10. Link de Pagamentos (`IPaymentLinksClient`)

Criação e gerenciamento de links de pagamento.

**Documentação**: [Link de Pagamentos - Asaas Docs](https://docs.asaas.com/reference/link-de-pagamentos)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `CreateAsync` | `POST /v3/paymentLinks` | Criar link de pagamento |
| `UpdateAsync` | `PUT /v3/paymentLinks/{id}` | Atualizar link de pagamento |
| `GetByIdAsync` | `GET /v3/paymentLinks/{id}` | Recuperar link específico |
| `ListAsync` | `GET /v3/paymentLinks` | Listar links de pagamento |
| `DeleteAsync` | `DELETE /v3/paymentLinks/{id}` | Remover link de pagamento |
| `RestoreAsync` | `POST /v3/paymentLinks/{id}/restore` | Restaurar link removido |
| `GetImageAsync` | `GET /v3/paymentLinks/{id}/images` | Obter imagens do link |
| `UpdateImageAsync` | `POST /v3/paymentLinks/{id}/images` | Atualizar imagem do link |
| `DeleteImageAsync` | `DELETE /v3/paymentLinks/{id}/images` | Remover imagem do link |
| `GetCheckoutConfigAsync` | `GET /v3/paymentLinks/{id}/checkoutConfig` | Obter configurações de checkout |
| `UpdateCheckoutConfigAsync` | `POST /v3/paymentLinks/{id}/checkoutConfig` | Atualizar configurações |

**Exemplo de uso**:
```csharp
/// <summary>
/// Cria um link de pagamento personalizado.
/// </summary>
/// <param name="request">Configurações do link</param>
/// <returns>Link de pagamento criado com URL</returns>
/// <see href="https://docs.asaas.com/reference/criar-um-link-de-pagamentos">Documentação Oficial</see>
var paymentLink = await _paymentLinksClient.CreateAsync(new PaymentLinkRequest
{
    Name = "Pagamento de Curso",
    Description = "Curso de C# Avançado",
    BillingType = BillingType.Undefined, // Permite o cliente escolher
    ChargeType = ChargeType.Detached,
    Value = 499.90m
});

Console.WriteLine($"URL do link: {paymentLink.Url}");
```

### 11. Informações Financeiras (`IFinanceClient`)

Consultas sobre saldo e estatísticas financeiras.

**Documentação**: [Informações Financeiras - Asaas Docs](https://docs.asaas.com/reference/informacoes-financeiras)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `GetBalanceAsync` | `GET /v3/finance/balance` | Recuperar saldo da conta |
| `GetPaymentStatisticsAsync` | `GET /v3/finance/payment/statistics` | Estatísticas de cobranças |
| `GetSplitStatisticsAsync` | `GET /v3/finance/split/statistics` | Estatísticas de split |

**Exemplo de uso**:
```csharp
/// <summary>
/// Recupera o saldo atual da conta no Asaas.
/// </summary>
/// <returns>Informações de saldo disponível e bloqueado</returns>
/// <see href="https://docs.asaas.com/reference/recuperar-saldo-da-conta">Documentação Oficial</see>
var balance = await _financeClient.GetBalanceAsync();

Console.WriteLine($"Saldo disponível: R$ {balance.Available}");
Console.WriteLine($"Saldo bloqueado: R$ {balance.Blocked}");
```

### 12. Extrato (`IFinancialTransactionsClient`)

Extrato de movimentações financeiras.

**Documentação**: [Extrato - Asaas Docs](https://docs.asaas.com/reference/extrato)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `ListAsync` | `GET /v3/financialTransactions` | Recuperar extrato |

### 13. Informações da Conta (`IAccountClient`)

Informações e personalização da conta.

**Documentação**: [Informações da Conta - Asaas Docs](https://docs.asaas.com/reference/informacoes-e-personalizacao-da-conta)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `GetWalletIdAsync` | `GET /v3/wallets` | Recuperar Wallet ID |
| `GetAccountInfoAsync` | `GET /v3/myAccount` | Recuperar dados comerciais |
| `SavePaymentCheckoutConfigAsync` | `POST /v3/myAccount/paymentCheckoutConfig` | Salvar personalização |
| `GetPaymentCheckoutConfigAsync` | `GET /v3/myAccount/paymentCheckoutConfig` | Recuperar configurações |
| `GetAccountNumberAsync` | `GET /v3/myAccount/accountNumber` | Recuperar número da conta |

### 14. Contas Asaas (Subcontas) (`ISubaccountsClient`)

Gerenciamento de subcontas.

**Documentação**: [Contas Asaas - Asaas Docs](https://docs.asaas.com/reference/contas-asaas)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `CreateAsync` | `POST /v3/accounts` | Criar conta |
| `ListAsync` | `GET /v3/accounts` | Listar contas |

### 15. Notificações (`INotificationsClient`)

Gerenciamento de notificações.

**Documentação**: [Notificações - Asaas Docs](https://docs.asaas.com/reference/notificacoes)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `UpdateAsync` | `POST /v3/notifications/{id}` | Atualizar notificação |
| `UpdateBatchAsync` | `POST /v3/notifications/batch` | Atualizar em lote |

### 16. Pagamento de Contas (`IBillPaymentsClient`)

Pagamento de boletos e contas.

**Documentação**: [Pagamento de Contas - Asaas Docs](https://docs.asaas.com/reference/pagamento-de-contas)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `CreateAsync` | `POST /v3/bill` | Criar pagamento de conta |
| `SimulateAsync` | `POST /v3/bill/simulate` | Simular pagamento |
| `GetByIdAsync` | `GET /v3/bill/{id}` | Recuperar pagamento |
| `ListAsync` | `GET /v3/bill` | Listar pagamentos de conta |
| `CancelAsync` | `POST /v3/bill/{id}/cancel` | Cancelar pagamento |

**Exemplo de uso**:
```csharp
/// <summary>
/// Realiza o pagamento de um boleto ou conta de consumo.
/// </summary>
/// <param name="request">Dados do pagamento</param>
/// <returns>Pagamento criado</returns>
/// <see href="https://docs.asaas.com/reference/criar-um-pagamento-de-conta">Documentação Oficial</see>
var billPayment = await _billPaymentsClient.CreateAsync(new BillPaymentRequest
{
    BankSlipIdentificationField = "34191.79001 01043.510047 91020.150008 1 93570000000100",
    ScheduleDate = DateTime.Now.AddDays(1),
    Description = "Pagamento de conta de luz"
});
```

### 17. Consulta SERASA (`ICreditBureauReportsClient`)

Consultas ao SERASA.

**Documentação**: [Consulta SERASA - Asaas Docs](https://docs.asaas.com/reference/consulta-serasa)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `CreateAsync` | `POST /v3/creditBureauReport` | Realizar consulta |
| `GetByIdAsync` | `GET /v3/creditBureauReport/{id}` | Recuperar consulta |
| `ListAsync` | `GET /v3/creditBureauReport` | Listar consultas |

### 18. Informações Fiscais (`ICustomerFiscalInfoClient`)

Informações fiscais de clientes.

**Documentação**: [Informações Fiscais - Asaas Docs](https://docs.asaas.com/reference/informacoes-fiscais)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `GetMunicipalOptionsAsync` | `GET /v3/customerFiscalInfo/municipalOptions` | Listar configurações municipais |
| `CreateOrUpdateAsync` | `POST /v3/customerFiscalInfo` | Criar/atualizar informações |
| `GetAsync` | `GET /v3/customerFiscalInfo` | Recuperar informações fiscais |

### 19. Webhooks de Cobrança (`IPaymentWebhooksClient`)

Configuração de webhooks para cobranças.

**Documentação**: [Webhooks - Asaas Docs](https://docs.asaas.com/reference/webhooks)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `CreateOrUpdateAsync` | `POST /v3/webhook` | Criar/atualizar webhook |
| `GetAsync` | `GET /v3/webhook` | Recuperar configurações |

**Exemplo de uso**:
```csharp
/// <summary>
/// Configura um webhook para receber notificações de cobranças.
/// </summary>
/// <param name="request">Configurações do webhook</param>
/// <returns>Configuração atualizada</returns>
/// <see href="https://docs.asaas.com/reference/criar-ou-atualizar-configuracoes-para-webhook">Documentação Oficial</see>
var webhook = await _paymentWebhooksClient.CreateOrUpdateAsync(new WebhookRequest
{
    Url = "https://seusite.com/webhooks/asaas",
    Email = "notificacoes@seusite.com",
    Enabled = true,
    ApiVersion = 3
});
```

### 20. Webhooks de Notas Fiscais (`IInvoiceWebhooksClient`)

Configuração de webhooks para notas fiscais.

**Documentação**: [Webhooks de NF - Asaas Docs](https://docs.asaas.com/reference/webhooks-de-notas-fiscais)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `CreateOrUpdateAsync` | `POST /v3/webhook/invoice` | Criar/atualizar webhook |
| `GetAsync` | `GET /v3/webhook/invoice` | Recuperar configuração |

### 21. Webhooks de Recarga de Celular (`IMobilePhoneRechargeWebhooksClient`)

Configuração de webhooks para recarga de celular.

**Documentação**: [Webhooks de Recarga - Asaas Docs](https://docs.asaas.com/reference/webhooks-de-recarga-de-celular)

| Método | Endpoint | Descrição |
|--------|----------|-----------|
| `CreateOrUpdateAsync` | `POST /v3/webhook/mobilePhoneRecharge` | Criar/atualizar webhook |
| `GetAsync` | `GET /v3/webhook/mobilePhoneRecharge` | Recuperar configuração |

## 📄 Paginação

A API do Asaas utiliza paginação baseada em `offset` e `limit`.

### Paginação Manual

```csharp
var request = new PageRequest
{
    Offset = 0,
    Limit = 10
};

var page = await _customersClient.ListAsync(request);

Console.WriteLine($"Total: {page.TotalCount}");
foreach (var customer in page.Data)
{
    Console.WriteLine($"- {customer.Name}");
}
```

### Paginação Automática

Use `ListAllAsync` para iterar sobre todas as páginas automaticamente:

```csharp
await foreach (var customer in _customersClient.ListAllAsync())
{
    Console.WriteLine($"Cliente: {customer.Name}");
    // Processa cada cliente
}
```

## ⚠️ Tratamento de Erros

### AsaasApiException

Todas as respostas de erro da API são encapsuladas em `AsaasApiException`:

```csharp
try
{
    var payment = await _paymentsClient.CreateAsync(request);
}
catch (AsaasApiException ex)
{
    Console.WriteLine($"Status Code: {ex.StatusCode}");
    Console.WriteLine($"Mensagem: {ex.Message}");
    
    if (ex.Errors != null)
    {
        foreach (var error in ex.Errors)
        {
            Console.WriteLine($"- Código: {error.Code}");
            Console.WriteLine($"  Descrição: {error.Description}");
        }
    }
}
catch (HttpRequestException ex)
{
    Console.WriteLine($"Erro de rede: {ex.Message}");
}
catch (TaskCanceledException ex)
{
    Console.WriteLine("Timeout na requisição");
}
```

### Códigos de Status Comuns

| Código | Descrição |
|--------|-----------|
| `200` | Sucesso |
| `400` | Requisição inválida |
| `401` | Não autenticado (API Key inválida) |
| `403` | Sem permissão |
| `404` | Recurso não encontrado |
| `429` | Rate limit excedido |
| `500` | Erro interno do servidor |

## 🛡️ Resiliência

A biblioteca implementa políticas de resiliência usando **Polly**:

### Retry com Backoff Exponencial

Requisições que falham com erros transitórios (408, 429, 5xx) são automaticamente repetidas:

```csharp
services.AddAsaas(options =>
{
    options.ApiKey = "sua-api-key";
    options.EnableRetry = true;
    options.MaxRetryAttempts = 3;
});
```

### Timeout

Configure o timeout global:

```csharp
services.AddAsaas(options =>
{
    options.Timeout = TimeSpan.FromSeconds(30);
});
```

### Circuit Breaker (Opcional)

```csharp
services.AddAsaas(options =>
{
    options.EnableCircuitBreaker = true;
    options.CircuitBreakerThreshold = 5;
    options.CircuitBreakerDuration = TimeSpan.FromMinutes(1);
});
```

### Rate Limiting

A biblioteca respeita automaticamente os headers de rate limit da API:

- `X-RateLimit-Limit`: Limite de requisições
- `X-RateLimit-Remaining`: Requisições restantes
- `Retry-After`: Tempo de espera para retry

## 🧪 Testes

A biblioteca possui cobertura de testes >= 90%.

### Executar Testes

```bash
# Todos os testes
dotnet test

# Com cobertura
dotnet test /p:CollectCoverage=true /p:CoverletOutputFormat=opencover

# Teste específico
dotnet test --filter "FullyQualifiedName~CustomersClientTests"
```

### Estrutura de Testes

```
tests/ZDZCode.Payments.Asaas.Tests/
├── Http/
│   └── AsaasHttpClientTests.cs
├── Errors/
│   └── AsaasApiExceptionTests.cs
├── Modules/
│   ├── Customers/
│   │   └── CustomersClientTests.cs
│   ├── Payments/
│   │   └── PaymentsClientTests.cs
│   └── ...
├── Pagination/
│   └── PaginationTests.cs
└── Options/
    └── AsaasOptionsValidationTests.cs
```

### Mocking HTTP

Os testes utilizam `MockHttpMessageHandler` para simular respostas da API:

```csharp
/// <summary>
/// Testa a criação de um cliente com sucesso.
/// </summary>
[Fact]
public async Task DeveCriarClienteComSucesso()
{
    // Arrange
    var mockHttp = new MockHttpMessageHandler();
    mockHttp.When("https://sandbox.asaas.com/api/v3/customers")
            .Respond("application/json", "{\"id\":\"cus_123\",\"name\":\"João\"}");
    
    var client = new CustomersClient(mockHttp.ToHttpClient());
    
    // Act
    var result = await client.CreateAsync(new CustomerRequest { Name = "João" });
    
    // Assert
    Assert.NotNull(result);
    Assert.Equal("cus_123", result.Id);
}
```

## 📖 Exemplos

Veja a pasta `samples/` para exemplos completos:

- **BasicUsage**: Criar clientes e cobranças
- **Subscriptions**: Criar e gerenciar assinaturas
- **Pix**: Operações com Pix
- **Webhooks**: Configurar e receber webhooks
- **Pagination**: Listar grandes volumes de dados
- **ErrorHandling**: Tratamento de erros

## 📚 Documentação da API Asaas

### Links Principais

- **Documentação Oficial**: [https://docs.asaas.com](https://docs.asaas.com)
- **Postman Collection**: [asaasPostmanCollection.json](https://raw.githubusercontent.com/asaasdev/postman-collection/main/asaasPostmanCollection.json)
- **Portal de Desenvolvedores**: [https://www.asaas.com/desenvolvedores](https://www.asaas.com/desenvolvedores)
- **Status da API**: [https://status.asaas.com](https://status.asaas.com)

### Documentação por Módulo

| Módulo | Link |
|--------|------|
| Clientes | [docs.asaas.com/reference/clientes](https://docs.asaas.com/reference/clientes) |
| Cobranças | [docs.asaas.com/reference/cobrancas](https://docs.asaas.com/reference/cobrancas) |
| Assinaturas | [docs.asaas.com/reference/assinaturas](https://docs.asaas.com/reference/assinaturas) |
| Antecipações | [docs.asaas.com/reference/antecipacoes](https://docs.asaas.com/reference/antecipacoes) |
| Parcelamentos | [docs.asaas.com/reference/parcelamentos](https://docs.asaas.com/reference/parcelamentos) |
| Negativações | [docs.asaas.com/reference/negativacoes](https://docs.asaas.com/reference/negativacoes) |
| Notas Fiscais | [docs.asaas.com/reference/notas-fiscais](https://docs.asaas.com/reference/notas-fiscais) |
| Pix | [docs.asaas.com/reference/pix](https://docs.asaas.com/reference/pix) |
| Transferências | [docs.asaas.com/reference/transferencias](https://docs.asaas.com/reference/transferencias) |
| Link de Pagamentos | [docs.asaas.com/reference/link-de-pagamentos](https://docs.asaas.com/reference/link-de-pagamentos) |
| Webhooks | [docs.asaas.com/reference/webhooks](https://docs.asaas.com/reference/webhooks) |
| Informações Financeiras | [docs.asaas.com/reference/informacoes-financeiras](https://docs.asaas.com/reference/informacoes-financeiras) |

## 🤝 Contribuindo

Contribuições são bem-vindas! Por favor, siga estas diretrizes:

1. Fork o repositório
2. Crie uma branch para sua feature (`git checkout -b feature/nova-funcionalidade`)
3. Commit suas mudanças (`git commit -m 'Adiciona nova funcionalidade'`)
4. Push para a branch (`git push origin feature/nova-funcionalidade`)
5. Abra um Pull Request

### Padrões de Código

- Seguir os padrões da ZDZCode
- Adicionar comentários XML (`<summary>`) em todos os métodos públicos
- Incluir links para documentação oficial do Asaas
- Manter cobertura de testes >= 90%
- Executar `dotnet format` antes de commitar

## 📝 Changelog

Veja [CHANGELOG.md](CHANGELOG.md) para histórico de versões.

## 📄 Licença

Este projeto está licenciado sob a Licença MIT - veja o arquivo [LICENSE](LICENSE) para detalhes.

## 🆘 Suporte

- **Issues**: [GitHub Issues](https://github.com/zdzcode/ZDZCode.Payments.Asaas/issues)
- **Email**: suporte@zdzcode.com
- **Documentação Asaas**: [docs.asaas.com](https://docs.asaas.com)

## 🎯 Roadmap

- [x] Implementação de todos os 118 endpoints
- [x] Testes unitários com cobertura >= 90%
- [x] Documentação completa
- [x] Pipeline CI/CD
- [ ] Exemplos adicionais
- [ ] Suporte a webhooks com validação de assinatura
- [ ] Cache de respostas (opcional)
- [ ] Métricas e telemetria avançada

---

**Desenvolvido com ❤️ pela equipe ZDZCode**
