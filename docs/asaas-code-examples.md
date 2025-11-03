# Exemplos de Código - ZDZCode.Payments.Asaas

Este documento contém exemplos práticos de implementação de cada módulo da biblioteca Asaas com comentários XML e links da documentação oficial.

## 📚 Índice

1. [Clientes](#clientes)
2. [Cobranças](#cobranças)
3. [Assinaturas](#assinaturas)
4. [Antecipações](#antecipações)
5. [Negativações](#negativações)
6. [Notas Fiscais](#notas-fiscais)
7. [Pix](#pix)
8. [Transferências](#transferências)
9. [Link de Pagamentos](#link-de-pagamentos)
10. [Webhooks](#webhooks)

---

## Clientes

### Interface Completa com Comentários

```csharp
namespace ZDZCode.Payments.Asaas.Clients
{
    /// <summary>
    /// Cliente para gerenciamento de clientes (pessoas físicas ou jurídicas) no Asaas.
    /// Permite criar, listar, atualizar e remover clientes da sua conta.
    /// </summary>
    /// <remarks>
    /// Documentação oficial: https://docs.asaas.com/reference/clientes
    /// </remarks>
    public interface ICustomersClient
    {
        /// <summary>
        /// Cria um novo cliente na sua conta Asaas.
        /// </summary>
        /// <param name="request">Dados do cliente a ser criado. Nome é obrigatório.</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Cliente criado com ID gerado pelo Asaas</returns>
        /// <exception cref="AsaasApiException">
        /// Lançada quando:
        /// - Nome não é fornecido (código: invalid_action)
        /// - CPF/CNPJ já cadastrado (código: customer_cpfCnpj_already_in_use)
        /// - E-mail inválido (código: invalid_email)
        /// </exception>
        /// <example>
        /// <code>
        /// var customer = await _customersClient.CreateAsync(new CustomerRequest
        /// {
        ///     Name = "João Silva",
        ///     Email = "joao@example.com",
        ///     CpfCnpj = "12345678901",
        ///     MobilePhone = "11987654321"
        /// });
        /// 
        /// Console.WriteLine($"Cliente criado com ID: {customer.Id}");
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/criar-novo-cliente">Criar novo cliente - Documentação Oficial</seealso>
        Task<CustomerResponse> CreateAsync(
            CustomerRequest request,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Recupera os dados de um cliente específico pelo ID.
        /// </summary>
        /// <param name="id">ID do cliente no formato "cus_000000000000"</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Dados completos do cliente</returns>
        /// <exception cref="AsaasApiException">
        /// Lançada quando o cliente não é encontrado (404)
        /// </exception>
        /// <example>
        /// <code>
        /// var customer = await _customersClient.GetByIdAsync("cus_000000000000");
        /// Console.WriteLine($"Cliente: {customer.Name}");
        /// Console.WriteLine($"E-mail: {customer.Email}");
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/recuperar-um-unico-cliente">Recuperar um único cliente - Documentação Oficial</seealso>
        Task<CustomerResponse> GetByIdAsync(
            string id,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Lista clientes com suporte a filtros e paginação.
        /// </summary>
        /// <param name="request">Filtros e parâmetros de paginação. Se null, usa valores padrão.</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Página de clientes com informações de paginação</returns>
        /// <remarks>
        /// Por padrão retorna 10 clientes por página. Máximo de 100 por página.
        /// Use o campo HasMore da resposta para verificar se há mais páginas.
        /// </remarks>
        /// <example>
        /// <code>
        /// // Buscar clientes com filtro
        /// var page = await _customersClient.ListAsync(new CustomerListRequest
        /// {
        ///     Name = "João",
        ///     Limit = 20,
        ///     Offset = 0
        /// });
        /// 
        /// Console.WriteLine($"Total encontrado: {page.TotalCount}");
        /// foreach (var customer in page.Data)
        /// {
        ///     Console.WriteLine($"- {customer.Name} ({customer.Email})");
        /// }
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/listar-clientes">Listar clientes - Documentação Oficial</seealso>
        Task<PageResponse<CustomerResponse>> ListAsync(
            CustomerListRequest? request = null,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Itera automaticamente sobre todas as páginas de clientes.
        /// Útil quando você precisa processar todos os clientes sem gerenciar paginação manualmente.
        /// </summary>
        /// <param name="request">Filtros opcionais</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Enumerável assíncrono de todos os clientes</returns>
        /// <remarks>
        /// Este método busca automaticamente as próximas páginas conforme você itera.
        /// Use com cuidado em listas muito grandes.
        /// </remarks>
        /// <example>
        /// <code>
        /// await foreach (var customer in _customersClient.ListAllAsync())
        /// {
        ///     Console.WriteLine($"Processando cliente: {customer.Name}");
        ///     // Processa cada cliente
        /// }
        /// </code>
        /// </example>
        IAsyncEnumerable<CustomerResponse> ListAllAsync(
            CustomerListRequest? request = null,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Atualiza os dados de um cliente existente.
        /// Apenas os campos enviados serão atualizados.
        /// </summary>
        /// <param name="id">ID do cliente a ser atualizado</param>
        /// <param name="request">Novos dados do cliente (campos não enviados não serão alterados)</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Cliente com dados atualizados</returns>
        /// <exception cref="AsaasApiException">
        /// Lançada quando:
        /// - Cliente não é encontrado (404)
        /// - E-mail inválido (400)
        /// </exception>
        /// <example>
        /// <code>
        /// // Atualizar apenas o e-mail
        /// var updated = await _customersClient.UpdateAsync("cus_000000000000", 
        ///     new CustomerRequest
        ///     {
        ///         Email = "novoemail@example.com"
        ///     });
        /// 
        /// Console.WriteLine($"E-mail atualizado para: {updated.Email}");
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/atualizar-cliente-existente">Atualizar cliente existente - Documentação Oficial</seealso>
        Task<CustomerResponse> UpdateAsync(
            string id,
            CustomerRequest request,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Remove um cliente (soft delete).
        /// O cliente pode ser restaurado posteriormente usando RestoreAsync.
        /// </summary>
        /// <param name="id">ID do cliente a ser removido</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <exception cref="AsaasApiException">
        /// Lançada quando:
        /// - Cliente não é encontrado (404)
        /// - Cliente possui cobranças pendentes (400)
        /// </exception>
        /// <remarks>
        /// Clientes com cobranças pendentes não podem ser removidos.
        /// É necessário cancelar ou receber todas as cobranças antes.
        /// </remarks>
        /// <example>
        /// <code>
        /// await _customersClient.DeleteAsync("cus_000000000000");
        /// Console.WriteLine("Cliente removido com sucesso");
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/remover-cliente">Remover cliente - Documentação Oficial</seealso>
        Task DeleteAsync(
            string id,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Restaura um cliente que foi removido anteriormente.
        /// </summary>
        /// <param name="id">ID do cliente a ser restaurado</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Cliente restaurado</returns>
        /// <exception cref="AsaasApiException">
        /// Lançada quando o cliente não está removido ou não é encontrado
        /// </exception>
        /// <example>
        /// <code>
        /// var restored = await _customersClient.RestoreAsync("cus_000000000000");
        /// Console.WriteLine($"Cliente {restored.Name} restaurado");
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/restaurar-cliente">Restaurar cliente - Documentação Oficial</seealso>
        Task<CustomerResponse> RestoreAsync(
            string id,
            CancellationToken cancellationToken = default);
    }
}
```

---

## Cobranças

### Interface com Comentários Detalhados

```csharp
namespace ZDZCode.Payments.Asaas.Clients
{
    /// <summary>
    /// Cliente para gerenciamento de cobranças no Asaas.
    /// Permite criar cobranças via boleto, cartão de crédito, Pix e outros meios de pagamento.
    /// </summary>
    /// <remarks>
    /// Documentação oficial: https://docs.asaas.com/reference/cobrancas
    /// </remarks>
    public interface IPaymentsClient
    {
        /// <summary>
        /// Cria uma nova cobrança no Asaas.
        /// Suporta boleto, cartão de crédito, Pix e outros meios de pagamento.
        /// </summary>
        /// <param name="request">Dados da cobrança incluindo cliente, valor e forma de pagamento</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Cobrança criada com ID, URL de pagamento e dados bancários</returns>
        /// <exception cref="AsaasApiException">
        /// Lançada quando:
        /// - Cliente não existe (código: invalid_customer)
        /// - Valor inválido (código: invalid_value)
        /// - Data de vencimento no passado (código: invalid_dueDate)
        /// - Forma de pagamento inválida (código: invalid_billingType)
        /// </exception>
        /// <example>
        /// <code>
        /// // Criar cobrança via boleto
        /// var payment = await _paymentsClient.CreateAsync(new PaymentRequest
        /// {
        ///     Customer = "cus_000000000000",
        ///     BillingType = BillingType.Boleto,
        ///     Value = 150.00m,
        ///     DueDate = DateTime.Now.AddDays(7),
        ///     Description = "Mensalidade Janeiro/2024"
        /// });
        /// 
        /// Console.WriteLine($"Cobrança criada: {payment.Id}");
        /// Console.WriteLine($"URL de pagamento: {payment.InvoiceUrl}");
        /// Console.WriteLine($"Linha digitável: {payment.BankSlipIdentificationField}");
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/criar-nova-cobranca">Criar nova cobrança - Documentação Oficial</seealso>
        Task<PaymentResponse> CreateAsync(
            PaymentRequest request,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Cria uma cobrança com pagamento via cartão de crédito.
        /// O pagamento é processado imediatamente e o valor é aprovado ou recusado na hora.
        /// </summary>
        /// <param name="request">Dados da cobrança incluindo informações do cartão</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Cobrança criada com status de aprovação/recusa</returns>
        /// <exception cref="AsaasApiException">
        /// Lançada quando:
        /// - Cartão inválido (código: invalid_credit_card)
        /// - Cartão recusado (código: credit_card_declined)
        /// - CVV inválido (código: invalid_cvv)
        /// </exception>
        /// <remarks>
        /// IMPORTANTE: Nunca armazene dados do cartão no seu sistema.
        /// Use sempre HTTPS para enviar dados sensíveis.
        /// Considere usar a tokenização de cartão para pagamentos recorrentes.
        /// </remarks>
        /// <example>
        /// <code>
        /// var payment = await _paymentsClient.CreateWithCreditCardAsync(new PaymentCreditCardRequest
        /// {
        ///     Customer = "cus_000000000000",
        ///     BillingType = BillingType.CreditCard,
        ///     Value = 99.90m,
        ///     DueDate = DateTime.Now,
        ///     CreditCard = new CreditCardInfo
        ///     {
        ///         HolderName = "João Silva",
        ///         Number = "5162306219378829",
        ///         ExpiryMonth = "12",
        ///         ExpiryYear = "2025",
        ///         Ccv = "123"
        ///     },
        ///     CreditCardHolderInfo = new CreditCardHolderInfo
        ///     {
        ///         Name = "João Silva",
        ///         Email = "joao@example.com",
        ///         CpfCnpj = "12345678901",
        ///         PostalCode = "01310-100",
        ///         AddressNumber = "100"
        ///     }
        /// });
        /// 
        /// if (payment.Status == PaymentStatus.Confirmed)
        /// {
        ///     Console.WriteLine("Pagamento aprovado!");
        /// }
        /// else
        /// {
        ///     Console.WriteLine($"Pagamento recusado: {payment.CreditCardMessage}");
        /// }
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/criar-cobranca-com-cartao-de-credito">Criar cobrança com cartão - Documentação Oficial</seealso>
        Task<PaymentResponse> CreateWithCreditCardAsync(
            PaymentCreditCardRequest request,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Cria uma cobrança parcelada (carnê).
        /// Gera múltiplas cobranças com vencimentos mensais.
        /// </summary>
        /// <param name="request">Dados da cobrança parcelada incluindo número de parcelas</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>ID do parcelamento criado</returns>
        /// <remarks>
        /// O valor de cada parcela é calculado automaticamente dividindo o valor total.
        /// Cada parcela gera uma cobrança individual que pode ser gerenciada separadamente.
        /// </remarks>
        /// <example>
        /// <code>
        /// var installment = await _paymentsClient.CreateInstallmentAsync(new InstallmentRequest
        /// {
        ///     Customer = "cus_000000000000",
        ///     BillingType = BillingType.Boleto,
        ///     Value = 300.00m,
        ///     InstallmentCount = 3, // 3 parcelas de R$ 100,00
        ///     InstallmentValue = 100.00m,
        ///     DueDate = DateTime.Now.AddDays(7)
        /// });
        /// 
        /// Console.WriteLine($"Parcelamento criado: {installment.Id}");
        /// Console.WriteLine($"Total de {installment.InstallmentCount} parcelas");
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/criar-uma-cobranca-parcelada">Criar cobrança parcelada - Documentação Oficial</seealso>
        Task<InstallmentResponse> CreateInstallmentAsync(
            InstallmentRequest request,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Obtém o QR Code Pix de uma cobrança.
        /// Disponível apenas para cobranças com BillingType = Pix.
        /// </summary>
        /// <param name="id">ID da cobrança</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Dados do QR Code incluindo payload e URL da imagem</returns>
        /// <exception cref="AsaasApiException">
        /// Lançada quando a cobrança não é do tipo Pix ou não está disponível
        /// </exception>
        /// <example>
        /// <code>
        /// var qrCode = await _paymentsClient.GetPixQrCodeAsync("pay_000000000000");
        /// 
        /// Console.WriteLine($"Payload Pix: {qrCode.Payload}");
        /// Console.WriteLine($"QR Code expira em: {qrCode.ExpirationDate}");
        /// 
        /// // Exibir URL da imagem para o cliente
        /// Console.WriteLine($"Imagem QR Code: {qrCode.EncodedImage}");
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/obter-qr-code-pix">Obter QR Code Pix - Documentação Oficial</seealso>
        Task<PixQrCodeResponse> GetPixQrCodeAsync(
            string id,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Estorna (reembolsa) uma cobrança já recebida.
        /// O valor é devolvido ao pagador.
        /// </summary>
        /// <param name="id">ID da cobrança a ser estornada</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Dados da cobrança com status de estorno</returns>
        /// <exception cref="AsaasApiException">
        /// Lançada quando:
        /// - Cobrança não foi recebida (código: payment_not_received)
        /// - Prazo de estorno expirado (código: refund_period_expired)
        /// - Saldo insuficiente para estorno (código: insufficient_balance)
        /// </exception>
        /// <remarks>
        /// Prazos para estorno:
        /// - Cartão de crédito: até 90 dias após o pagamento
        /// - Pix: até 90 dias após o pagamento
        /// - Boleto: não é possível estornar, apenas fazer transferência manual
        /// </remarks>
        /// <example>
        /// <code>
        /// try
        /// {
        ///     var refunded = await _paymentsClient.RefundAsync("pay_000000000000");
        ///     Console.WriteLine($"Estorno realizado. Status: {refunded.Status}");
        /// }
        /// catch (AsaasApiException ex) when (ex.StatusCode == HttpStatusCode.BadRequest)
        /// {
        ///     Console.WriteLine($"Não foi possível estornar: {ex.Message}");
        /// }
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/estornar-cobranca">Estornar cobrança - Documentação Oficial</seealso>
        Task<PaymentResponse> RefundAsync(
            string id,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Confirma o recebimento de uma cobrança paga em dinheiro.
        /// Use quando o cliente pagar diretamente em dinheiro.
        /// </summary>
        /// <param name="id">ID da cobrança</param>
        /// <param name="paymentDate">Data do recebimento (opcional, usa data atual se não fornecida)</param>
        /// <param name="value">Valor recebido (opcional, usa valor da cobrança se não fornecido)</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Cobrança com status atualizado para recebida</returns>
        /// <remarks>
        /// Esta operação NÃO movimenta valores na conta Asaas.
        /// É apenas para controle e baixa da cobrança.
        /// </remarks>
        /// <example>
        /// <code>
        /// var received = await _paymentsClient.ConfirmCashReceiptAsync(
        ///     "pay_000000000000",
        ///     paymentDate: DateTime.Now,
        ///     value: 150.00m
        /// );
        /// 
        /// Console.WriteLine($"Cobrança marcada como recebida em dinheiro");
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/confirmar-recebimento-em-dinheiro">Confirmar recebimento em dinheiro - Documentação Oficial</seealso>
        Task<PaymentResponse> ConfirmCashReceiptAsync(
            string id,
            DateTime? paymentDate = null,
            decimal? value = null,
            CancellationToken cancellationToken = default);
    }
}
```

---

## Pix

### Interface com Comentários sobre Pix

```csharp
namespace ZDZCode.Payments.Asaas.Clients
{
    /// <summary>
    /// Cliente para operações Pix no Asaas.
    /// Permite criar QR Codes, consultar transações e gerenciar chaves Pix.
    /// </summary>
    /// <remarks>
    /// O Pix é um meio de pagamento instantâneo do Banco Central do Brasil.
    /// Documentação oficial: https://docs.asaas.com/reference/pix
    /// </remarks>
    public interface IPixClient
    {
        /// <summary>
        /// Cria um QR Code Pix estático para recebimento de pagamentos.
        /// QR Codes estáticos podem ser reutilizados e não têm valor fixo.
        /// </summary>
        /// <param name="request">Configurações do QR Code incluindo chave Pix e valor (opcional)</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>QR Code criado com payload Pix e URL da imagem</returns>
        /// <exception cref="AsaasApiException">
        /// Lançada quando:
        /// - Chave Pix inválida ou não pertence à conta (código: invalid_pix_key)
        /// - Valor inválido (código: invalid_value)
        /// </exception>
        /// <remarks>
        /// QR Codes estáticos:
        /// - Podem ser usados múltiplas vezes
        /// - Não expiram
        /// - Podem ter valor fixo ou permitir que o pagador digite o valor
        /// - Ideais para pontos de venda físicos
        /// </remarks>
        /// <example>
        /// <code>
        /// // QR Code com valor fixo
        /// var qrCode = await _pixClient.CreateStaticQrCodeAsync(new PixQrCodeRequest
        /// {
        ///     AddressKey = "suachave@pix.com.br",
        ///     Description = "Pagamento de produtos",
        ///     Value = 50.00m,
        ///     AllowsMultiplePayments = true
        /// });
        /// 
        /// Console.WriteLine($"Payload Pix: {qrCode.Payload}");
        /// Console.WriteLine($"Copie e cole: {qrCode.Payload}");
        /// 
        /// // Exibir QR Code como imagem
        /// Console.WriteLine($"URL da imagem: {qrCode.ImageUrl}");
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/criar-qrcode-estatico">Criar QR Code estático - Documentação Oficial</seealso>
        Task<PixQrCodeResponse> CreateStaticQrCodeAsync(
            PixQrCodeRequest request,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Decodifica um QR Code Pix para visualizar seus dados sem efetuar pagamento.
        /// Útil para validar QR Codes antes de pagar.
        /// </summary>
        /// <param name="payload">String do QR Code Pix (começa com "00020126...")</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Informações decodificadas do QR Code incluindo valor, beneficiário e descrição</returns>
        /// <exception cref="AsaasApiException">
        /// Lançada quando o payload é inválido (código: invalid_pix_payload)
        /// </exception>
        /// <example>
        /// <code>
        /// var decoded = await _pixClient.DecodeQrCodeAsync(
        ///     "00020126580014br.gov.bcb.pix0136..."
        /// );
        /// 
        /// Console.WriteLine($"Beneficiário: {decoded.RecipientName}");
        /// Console.WriteLine($"Valor: R$ {decoded.Value}");
        /// Console.WriteLine($"Descrição: {decoded.Description}");
        /// Console.WriteLine($"Chave: {decoded.AddressKey}");
        /// 
        /// // Perguntar ao usuário se deseja confirmar o pagamento
        /// if (ConfirmPayment())
        /// {
        ///     // Processar pagamento...
        /// }
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/decodificar-um-qrcode">Decodificar QR Code - Documentação Oficial</seealso>
        Task<PixDecodedQrCodeResponse> DecodeQrCodeAsync(
            string payload,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Lista todas as transações Pix da conta.
        /// Inclui pagamentos recebidos e enviados.
        /// </summary>
        /// <param name="request">Filtros de data, status e paginação</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Página de transações Pix</returns>
        /// <remarks>
        /// Tipos de transação:
        /// - CREDIT: Recebimento (entrada de dinheiro)
        /// - DEBIT: Pagamento (saída de dinheiro)
        /// 
        /// Status possíveis:
        /// - PENDING: Aguardando processamento
        /// - CONFIRMED: Confirmada
        /// - FAILED: Falhou
        /// - CANCELLED: Cancelada (apenas para transações agendadas)
        /// </remarks>
        /// <example>
        /// <code>
        /// // Listar transações do último mês
        /// var transactions = await _pixClient.ListTransactionsAsync(new PixTransactionListRequest
        /// {
        ///     DateCreatedGe = DateTime.Now.AddMonths(-1),
        ///     DateCreatedLe = DateTime.Now,
        ///     Limit = 50
        /// });
        /// 
        /// Console.WriteLine($"Total de transações: {transactions.TotalCount}");
        /// 
        /// decimal totalReceived = 0;
        /// decimal totalSent = 0;
        /// 
        /// foreach (var transaction in transactions.Data)
        /// {
        ///     if (transaction.Type == PixTransactionType.Credit)
        ///     {
        ///         totalReceived += transaction.Value;
        ///         Console.WriteLine($"[RECEBIDO] R$ {transaction.Value} de {transaction.OriginName}");
        ///     }
        ///     else
        ///     {
        ///         totalSent += transaction.Value;
        ///         Console.WriteLine($"[ENVIADO] R$ {transaction.Value} para {transaction.DestinationName}");
        ///     }
        /// }
        /// 
        /// Console.WriteLine($"\nTotal recebido: R$ {totalReceived}");
        /// Console.WriteLine($"Total enviado: R$ {totalSent}");
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/listar-transacoes">Listar transações - Documentação Oficial</seealso>
        Task<PageResponse<PixTransactionResponse>> ListTransactionsAsync(
            PixTransactionListRequest? request = null,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Cancela uma transação Pix agendada.
        /// Somente transações com status PENDING podem ser canceladas.
        /// </summary>
        /// <param name="id">ID da transação a ser cancelada</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Transação com status atualizado para CANCELLED</returns>
        /// <exception cref="AsaasApiException">
        /// Lançada quando:
        /// - Transação não está agendada (código: transaction_not_scheduled)
        /// - Transação já foi processada (código: transaction_already_processed)
        /// </exception>
        /// <example>
        /// <code>
        /// try
        /// {
        ///     var cancelled = await _pixClient.CancelScheduledTransactionAsync("pix_000000000000");
        ///     Console.WriteLine($"Transação cancelada. Status: {cancelled.Status}");
        /// }
        /// catch (AsaasApiException ex)
        /// {
        ///     Console.WriteLine($"Não foi possível cancelar: {ex.Message}");
        /// }
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/cancelar-uma-transacao-agendada">Cancelar transação agendada - Documentação Oficial</seealso>
        Task<PixTransactionResponse> CancelScheduledTransactionAsync(
            string id,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Lista todas as chaves Pix cadastradas na conta.
        /// </summary>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Lista de chaves Pix</returns>
        /// <remarks>
        /// Tipos de chave Pix:
        /// - CPF/CNPJ
        /// - E-mail
        /// - Telefone celular
        /// - Chave aleatória (EVP)
        /// </remarks>
        /// <example>
        /// <code>
        /// var keys = await _pixClient.ListAddressKeysAsync();
        /// 
        /// Console.WriteLine("Chaves Pix cadastradas:");
        /// foreach (var key in keys)
        /// {
        ///     Console.WriteLine($"- {key.Key} ({key.Type})");
        ///     Console.WriteLine($"  Status: {key.Status}");
        ///     Console.WriteLine($"  Cadastrada em: {key.DateCreated:dd/MM/yyyy}");
        /// }
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/listar-chaves-pix">Listar chaves Pix - Documentação Oficial</seealso>
        Task<List<PixAddressKeyResponse>> ListAddressKeysAsync(
            CancellationToken cancellationToken = default);
    }
}
```

---

## Webhooks

### Interface com Comentários sobre Webhooks

```csharp
namespace ZDZCode.Payments.Asaas.Clients
{
    /// <summary>
    /// Cliente para configuração de webhooks de cobranças no Asaas.
    /// Webhooks permitem receber notificações em tempo real sobre eventos de cobranças.
    /// </summary>
    /// <remarks>
    /// Eventos suportados:
    /// - PAYMENT_CREATED: Nova cobrança criada
    /// - PAYMENT_UPDATED: Cobrança atualizada
    /// - PAYMENT_CONFIRMED: Pagamento confirmado
    /// - PAYMENT_RECEIVED: Pagamento recebido
    /// - PAYMENT_OVERDUE: Cobrança vencida
    /// - PAYMENT_DELETED: Cobrança removida
    /// - PAYMENT_RESTORED: Cobrança restaurada
    /// - PAYMENT_REFUNDED: Cobrança estornada
    /// - PAYMENT_RECEIVED_IN_CASH_UNDONE: Recebimento em dinheiro desfeito
    /// - PAYMENT_CHARGEBACK_REQUESTED: Chargeback solicitado
    /// - PAYMENT_CHARGEBACK_DISPUTE: Contestação de chargeback
    /// - PAYMENT_AWAITING_CHARGEBACK_REVERSAL: Aguardando reversão de chargeback
    /// - PAYMENT_DUNNING_RECEIVED: Negativação recebida
    /// - PAYMENT_DUNNING_REQUESTED: Negativação solicitada
    /// - PAYMENT_BANK_SLIP_VIEWED: Boleto visualizado
    /// 
    /// Documentação oficial: https://docs.asaas.com/reference/webhooks
    /// </remarks>
    public interface IPaymentWebhooksClient
    {
        /// <summary>
        /// Cria ou atualiza a configuração de webhook para cobranças.
        /// Se já existe configuração, ela será sobrescrita.
        /// </summary>
        /// <param name="request">Configurações do webhook incluindo URL e eventos</param>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Configuração do webhook criada ou atualizada</returns>
        /// <exception cref="AsaasApiException">
        /// Lançada quando:
        /// - URL inválida (código: invalid_url)
        /// - URL não é HTTPS (código: url_must_be_https)
        /// - Eventos inválidos (código: invalid_events)
        /// </exception>
        /// <remarks>
        /// IMPORTANTE:
        /// - A URL deve ser HTTPS
        /// - A URL deve estar acessível publicamente
        /// - Responda sempre com HTTP 200 para confirmar recebimento
        /// - Implemente validação de assinatura para segurança
        /// - Configure retry em caso de falhas temporárias
        /// 
        /// O Asaas envia um POST com os dados do evento no corpo da requisição.
        /// Header "asaas-access-token" contém um token para validação.
        /// 
        /// Tentativas de entrega:
        /// - Total de 10 tentativas
        /// - Intervalo crescente entre tentativas (1min, 5min, 15min, 1h, etc.)
        /// - Após 10 falhas, o webhook é desabilitado automaticamente
        /// </remarks>
        /// <example>
        /// <code>
        /// // Configurar webhook para receber notificações de pagamento
        /// var webhook = await _webhooksClient.CreateOrUpdateAsync(new WebhookRequest
        /// {
        ///     Url = "https://seusite.com/webhooks/asaas/payments",
        ///     Email = "notificacoes@seusite.com", // E-mail para alertas de falhas
        ///     Enabled = true,
        ///     ApiVersion = 3,
        ///     Interrupted = false,
        ///     AuthToken = "seu-token-secreto-para-validacao"
        /// });
        /// 
        /// Console.WriteLine($"Webhook configurado: {webhook.Url}");
        /// Console.WriteLine($"Status: {(webhook.Enabled ? "Ativo" : "Inativo")}");
        /// </code>
        /// </example>
        /// <example>
        /// <code>
        /// // Exemplo de endpoint ASP.NET Core para receber webhooks
        /// [ApiController]
        /// [Route("webhooks/asaas")]
        /// public class AsaasWebhookController : ControllerBase
        /// {
        ///     [HttpPost("payments")]
        ///     public async Task<IActionResult> ReceivePaymentWebhook(
        ///         [FromBody] AsaasWebhookPayload payload,
        ///         [FromHeader(Name = "asaas-access-token")] string accessToken)
        ///     {
        ///         // 1. Validar o token
        ///         if (!ValidateToken(accessToken))
        ///         {
        ///             return Unauthorized();
        ///         }
        ///         
        ///         // 2. Processar o evento
        ///         try
        ///         {
        ///             switch (payload.Event)
        ///             {
        ///                 case "PAYMENT_CONFIRMED":
        ///                     await ProcessPaymentConfirmed(payload.Payment);
        ///                     break;
        ///                     
        ///                 case "PAYMENT_RECEIVED":
        ///                     await ProcessPaymentReceived(payload.Payment);
        ///                     break;
        ///                     
        ///                 case "PAYMENT_OVERDUE":
        ///                     await ProcessPaymentOverdue(payload.Payment);
        ///                     break;
        ///                     
        ///                 // ... outros eventos
        ///             }
        ///             
        ///             // 3. Retornar 200 para confirmar recebimento
        ///             return Ok();
        ///         }
        ///         catch (Exception ex)
        ///         {
        ///             // Logar erro mas retornar 200 para evitar reenvios
        ///             _logger.LogError(ex, "Erro ao processar webhook");
        ///             return Ok();
        ///         }
        ///     }
        /// }
        /// 
        /// public class AsaasWebhookPayload
        /// {
        ///     public string Event { get; set; }
        ///     public PaymentWebhookData Payment { get; set; }
        /// }
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/criar-ou-atualizar-configuracoes-para-webhook">Configurar webhook - Documentação Oficial</seealso>
        /// <seealso href="https://docs.asaas.com/docs/webhooks-1">Guia completo de webhooks - Asaas</seealso>
        Task<WebhookResponse> CreateOrUpdateAsync(
            WebhookRequest request,
            CancellationToken cancellationToken = default);

        /// <summary>
        /// Recupera a configuração atual do webhook de cobranças.
        /// </summary>
        /// <param name="cancellationToken">Token para cancelamento da operação</param>
        /// <returns>Configuração do webhook ou null se não configurado</returns>
        /// <example>
        /// <code>
        /// var config = await _webhooksClient.GetAsync();
        /// 
        /// if (config != null)
        /// {
        ///     Console.WriteLine($"Webhook URL: {config.Url}");
        ///     Console.WriteLine($"Status: {(config.Enabled ? "Ativo" : "Inativo")}");
        ///     Console.WriteLine($"Versão da API: {config.ApiVersion}");
        ///     
        ///     if (config.Interrupted)
        ///     {
        ///         Console.WriteLine("ATENÇÃO: Webhook foi interrompido por falhas!");
        ///         Console.WriteLine($"E-mail de notificação: {config.Email}");
        ///     }
        /// }
        /// else
        /// {
        ///     Console.WriteLine("Nenhum webhook configurado");
        /// }
        /// </code>
        /// </example>
        /// <seealso href="https://docs.asaas.com/reference/recuperar-configuracoes-de-webhook">Recuperar configurações - Documentação Oficial</seealso>
        Task<WebhookResponse?> GetAsync(
            CancellationToken cancellationToken = default);
    }
}
```

---

## Boas Práticas de Comentários

### Estrutura Recomendada

```csharp
/// <summary>
/// [Descrição breve do que o método faz em uma linha]
/// </summary>
/// <param name="parametro1">[Descrição do parâmetro]</param>
/// <param name="parametro2">[Descrição do parâmetro]</param>
/// <param name="cancellationToken">Token para cancelamento da operação</param>
/// <returns>[O que o método retorna]</returns>
/// <exception cref="TipoException">
/// Lançada quando:
/// - [Cenário 1] (código: codigo_erro_1)
/// - [Cenário 2] (código: codigo_erro_2)
/// </exception>
/// <remarks>
/// [Informações adicionais importantes]
/// [Restrições]
/// [Comportamentos especiais]
/// </remarks>
/// <example>
/// <code>
/// // [Exemplo de código funcional]
/// var resultado = await metodo();
/// Console.WriteLine(resultado);
/// </code>
/// </example>
/// <seealso href="https://docs.asaas.com/reference/endpoint">Nome do Endpoint - Documentação Oficial</seealso>
```

### Checklist de Documentação

Para cada método público:
- [ ] Tag `<summary>` com descrição clara e concisa
- [ ] Tags `<param>` para todos os parâmetros
- [ ] Tag `<returns>` descrevendo o retorno
- [ ] Tag `<exception>` para exceções conhecidas com códigos de erro
- [ ] Tag `<remarks>` com informações adicionais importantes
- [ ] Tag `<example>` com código funcional e comentado
- [ ] Tag `<seealso>` com link da documentação oficial do Asaas
- [ ] Mencionar códigos de erro específicos quando aplicável
- [ ] Incluir avisos de segurança quando relevante (dados sensíveis, HTTPS, etc.)
- [ ] Documentar comportamentos especiais (rate limits, prazos, validações)

---

## Links de Referência por Módulo

| Módulo | Documentação Oficial |
|--------|---------------------|
| Clientes | https://docs.asaas.com/reference/clientes |
| Cobranças | https://docs.asaas.com/reference/cobrancas |
| Assinaturas | https://docs.asaas.com/reference/assinaturas |
| Antecipações | https://docs.asaas.com/reference/antecipacoes |
| Parcelamentos | https://docs.asaas.com/reference/parcelamentos |
| Negativações | https://docs.asaas.com/reference/negativacoes |
| Notas Fiscais | https://docs.asaas.com/reference/notas-fiscais |
| Pix | https://docs.asaas.com/reference/pix |
| Transferências | https://docs.asaas.com/reference/transferencias |
| Link de Pagamentos | https://docs.asaas.com/reference/link-de-pagamentos |
| Webhooks | https://docs.asaas.com/reference/webhooks |
| Guia de Webhooks | https://docs.asaas.com/docs/webhooks-1 |
| Informações Financeiras | https://docs.asaas.com/reference/informacoes-financeiras |
| Extrato | https://docs.asaas.com/reference/extrato |
| Informações da Conta | https://docs.asaas.com/reference/informacoes-e-personalizacao-da-conta |
| Contas Asaas | https://docs.asaas.com/reference/contas-asaas |
| Notificações | https://docs.asaas.com/reference/notificacoes |
| Pagamento de Contas | https://docs.asaas.com/reference/pagamento-de-contas |
| Consulta SERASA | https://docs.asaas.com/reference/consulta-serasa |
| Informações Fiscais | https://docs.asaas.com/reference/informacoes-fiscais |

---

**Nota**: Todos os exemplos de código neste documento são funcionais e seguem as melhores práticas da ZDZCode.
