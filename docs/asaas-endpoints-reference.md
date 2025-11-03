# Referência Completa de Endpoints - Asaas API

Este documento mapeia todos os 118 endpoints da API do Asaas implementados na biblioteca ZDZCode.Payments.Asaas.

**Fonte**: [Coleção Postman Oficial do Asaas](https://raw.githubusercontent.com/asaasdev/postman-collection/main/asaasPostmanCollection.json)

**Total de Endpoints**: 118  
**Total de Módulos**: 21

---

## Índice de Módulos

1. [Antecipações](#antecipacoes) (4 endpoints)
2. [Assinaturas](#assinaturas) (13 endpoints)
3. [Clientes](#clientes) (6 endpoints)
4. [Cobranças](#cobrancas) (15 endpoints)
5. [Consulta SERASA](#consulta-serasa) (3 endpoints)
6. [Contas Asaas](#contas-asaas) (2 endpoints)
7. [Extrato](#extrato) (1 endpoints)
8. [Informações e personalização da conta](#informacoes-e-personalizacao-da-conta) (5 endpoints)
9. [Informações financeiras](#informacoes-financeiras) (3 endpoints)
10. [Informações fiscais](#informacoes-fiscais) (3 endpoints)
11. [Link de Pagamentos](#link-de-pagamentos) (11 endpoints)
12. [Negativações](#negativacoes) (9 endpoints)
13. [Notas fiscais](#notas-fiscais) (7 endpoints)
14. [Notificações](#notificacoes) (2 endpoints)
15. [Pagamento de contas](#pagamento-de-contas) (5 endpoints)
16. [Parcelamentos](#parcelamentos) (6 endpoints)
17. [Pix](#pix) (10 endpoints)
18. [Transferências](#transferências) (7 endpoints)
19. [Webhook de Cobrança](#webhook-de-cobranca) (2 endpoints)
20. [Webhook de Notas Fiscais](#webhook-de-notas-fiscais) (2 endpoints)
21. [Webhook de Recarga de celular](#webhook-de-recarga-de-celular) (2 endpoints)

---

## Antecipações

**Total de Endpoints**: 4

**Documentação Oficial**: [https://docs.asaas.com/reference/antecipacoes](https://docs.asaas.com/reference/antecipacoes)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `POST` | `/v3/anticipations` | Solicitar antecipação | `RequestAsync` |
| 2 | `POST` | `/v3/anticipations/simulate` | Simular antecipação | `SimulateAsync` |
| 3 | `GET` | `/v3/anticipations/{{id_da_antecipacao}}` | Recuperar uma única antecipação | `GetByIdAsync` |
| 4 | `GET` | `/v3/anticipations` | Listar antecipações | `ListAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Solicitar antecipação
/// </summary>
/// <see href="https://docs.asaas.com/reference/antecipacoes">Documentação Oficial</see>
var request = new Request();
var result = await client.CreateAsync(request);
```

---

## Assinaturas

**Total de Endpoints**: 13

**Documentação Oficial**: [https://docs.asaas.com/reference/assinaturas](https://docs.asaas.com/reference/assinaturas)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `POST` | `/v3/subscriptions` | Criar nova assinatura | `CreateAsync` |
| 2 | `GET` | `/v3/subscriptions/{{id_da_assinatura}}` | Recuperar uma única assinatura | `GetByIdAsync` |
| 3 | `GET` | `/v3/subscriptions` | Listar assinaturas | `ListAsync` |
| 4 | `GET` | `/v3/subscriptions/{{id_da_assinatura}}/payments` | Listar cobranças de uma assinatura | `ListAsync` |
| 5 | `GET` | `/v3/subscriptions/{{id_da_assinatura}}/paymentBook` | Listar carnê de parcelamento de assinatura | `ListAsync` |
| 6 | `POST` | `/v3/subscriptions` | Criar assinatura com cartão de crédito | `CreateAsync` |
| 7 | `POST` | `/v3/subscriptions/{{id_da_assinatura}}` | Atualizar assinatura existente | `UpdateAsync` |
| 8 | `DELETE` | `/v3/subscriptions/{{id_da_assinatura}}` | Remover assinatura | `DeleteAsync` |
| 9 | `GET` | `/v3/subscriptions/{{id_da_assinatura}}/invoices` | Listar notas fiscais de uma assinatura | `ListAsync` |
| 10 | `POST` | `/v3/subscriptions/{{id_da_assinatura}}/invoiceSettings` | Criar configurações para emissão de notas fiscais | `CreateAsync` |
| 11 | `POST` | `/v3/subscriptions/{{id_da_assinatura}}/invoiceSettings` | Atualizar configuração para emissão de notas fiscais | `UpdateAsync` |
| 12 | `GET` | `/v3/subscriptions/{{id_da_assinatura}}/invoiceSettings` | Recuperar configuração para emissão de notas fiscais | `GetByIdAsync` |
| 13 | `DELETE` | `/v3/subscriptions/{{id_da_assinatura}}/invoiceSettings` | Remover configuração para emissão de notas fiscais | `DeleteAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Criar nova assinatura
/// </summary>
/// <see href="https://docs.asaas.com/reference/assinaturas">Documentação Oficial</see>
var request = new Request();
var result = await client.CreateAsync(request);
```

---

## Clientes

**Total de Endpoints**: 6

**Documentação Oficial**: [https://docs.asaas.com/reference/clientes](https://docs.asaas.com/reference/clientes)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `POST` | `/v3/customers` | Criar novo cliente | `CreateAsync` |
| 2 | `GET` | `/v3/customers/{{id_do_cliente}}` | Recuperar um único cliente | `GetByIdAsync` |
| 3 | `GET` | `/v3/customers` | Listar clientes | `ListAsync` |
| 4 | `POST` | `/v3/customers/{{id_do_cliente}}` | Atualizar cliente existente | `UpdateAsync` |
| 5 | `DELETE` | `/v3/customers/{{id_do_cliente}}` | Remover cliente | `DeleteAsync` |
| 6 | `POST` | `/v3/customers/{{id_do_cliente}}/restore` | Restaurar cliente removido | `RestoreAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Criar novo cliente
/// </summary>
/// <see href="https://docs.asaas.com/reference/clientes">Documentação Oficial</see>
var request = new Request();
var result = await client.CreateAsync(request);
```

---

## Cobranças

**Total de Endpoints**: 15

**Documentação Oficial**: [https://docs.asaas.com/reference/cobrancas](https://docs.asaas.com/reference/cobrancas)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `POST` | `/v3/payments` | Criar nova cobrança | `CreateAsync` |
| 2 | `POST` | `/v3/payments` | Criar cobrança com cartão de crédito | `CreateAsync` |
| 3 | `POST` | `/v3/payments` | Criar uma cobrança parcelada | `CreateAsync` |
| 4 | `POST` | `/v3/payments` | Criar cobrança com split | `CreateAsync` |
| 5 | `GET` | `/v3/payments/{{id_da_cobranca}}` | Recuperar uma única cobrança | `GetByIdAsync` |
| 6 | `GET` | `/v3/payments` | Listar cobranças | `ListAsync` |
| 7 | `POST` | `/v3/payments/{{id_da_cobranca}}` | Atualizar cobrança existente | `UpdateAsync` |
| 8 | `DELETE` | `/v3/payments/{{id_da_cobranca}}` | Remover cobrança | `DeleteAsync` |
| 9 | `POST` | `/v3/payments/{{id_da_cobranca}}/restore` | Restaurar cobrança removida | `RestoreAsync` |
| 10 | `POST` | `/v3/payments/{{id_da_cobranca}}/refund` | Estornar cobrança | `RefundAsync` |
| 11 | `GET` | `/v3/payments` | Obter linha digitável do boleto | `GetAsync` |
| 12 | `GET` | `/v3/payments/{{id_da_cobranca}}/pixQrCode` | Obter QR Code PIX | `GetByIdAsync` |
| 13 | `POST` | `/v3/payments/{{id_da_cobranca}}/receiveInCash` | Confirmar recebimento em dinheiro | `ProcessAsync` |
| 14 | `POST` | `/v3/payments/{{id_da_cobranca}}/undoReceivedInCash` | Desfazer confirmação de recebimento em dinheiro | `ProcessAsync` |
| 15 | `POST` | `/v3/payments/simulate` | Simulador de taxas | `ProcessAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Criar nova cobrança
/// </summary>
/// <see href="https://docs.asaas.com/reference/cobrancas">Documentação Oficial</see>
var request = new Request();
var result = await client.CreateAsync(request);
```

---

## Consulta SERASA

**Total de Endpoints**: 3

**Documentação Oficial**: [https://docs.asaas.com/reference/consulta-serasa](https://docs.asaas.com/reference/consulta-serasa)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `POST` | `/v3/creditBureauReport` | Realizar consulta | `ProcessAsync` |
| 2 | `GET` | `/v3/creditBureauReport/{{id_da_consulta_serasa}}` | Recuperar uma consulta | `GetByIdAsync` |
| 3 | `GET` | `/v3/creditBureauReport` | Listar consultas | `ListAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Realizar consulta
/// </summary>
/// <see href="https://docs.asaas.com/reference/consulta-serasa">Documentação Oficial</see>
var request = new Request();
var result = await client.CreateAsync(request);
```

---

## Contas Asaas

**Total de Endpoints**: 2

**Documentação Oficial**: [https://docs.asaas.com/reference/contas-asaas](https://docs.asaas.com/reference/contas-asaas)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `POST` | `/v3/accounts` | Criar conta | `CreateAsync` |
| 2 | `GET` | `/v3/accounts` | Listar contas | `ListAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Criar conta
/// </summary>
/// <see href="https://docs.asaas.com/reference/contas-asaas">Documentação Oficial</see>
var request = new Request();
var result = await client.CreateAsync(request);
```

---

## Extrato

**Total de Endpoints**: 1

**Documentação Oficial**: [https://docs.asaas.com/reference/extrato](https://docs.asaas.com/reference/extrato)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `GET` | `/v3/financialTransactions` | Recuperar extrato | `GetAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Recuperar extrato
/// </summary>
/// <see href="https://docs.asaas.com/reference/extrato">Documentação Oficial</see>
var result = await client.ListAsync();
```

---

## Informações e personalização da conta

**Total de Endpoints**: 5

**Documentação Oficial**: [https://docs.asaas.com/reference/informacoes-e-personalizacao-da-conta](https://docs.asaas.com/reference/informacoes-e-personalizacao-da-conta)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `GET` | `/v3/wallets` | Recuperar WalletId | `GetAsync` |
| 2 | `GET` | `/v3/myAccount/` | Recuperar dados comerciais | `GetAsync` |
| 3 | `POST` | `/v3/myAccount/paymentCheckoutConfig/` | Salvar personalização da fatura | `UpdateAsync` |
| 4 | `GET` | `/v3/myAccount/paymentCheckoutConfig/` | Recuperar configurações de personalização | `GetAsync` |
| 5 | `GET` | `/v3/myAccount/accountNumber/` | Recuperar número de conta no Asaas | `GetAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Recuperar WalletId
/// </summary>
/// <see href="https://docs.asaas.com/reference/informacoes-e-personalizacao-da-conta">Documentação Oficial</see>
var result = await client.ListAsync();
```

---

## Informações financeiras

**Total de Endpoints**: 3

**Documentação Oficial**: [https://docs.asaas.com/reference/informacoes-financeiras](https://docs.asaas.com/reference/informacoes-financeiras)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `GET` | `/v3/finance/balance` | Recuperar saldo da conta | `GetAsync` |
| 2 | `GET` | `/v3/finance/payment/statistics` | Estatísticas de cobranças | `ProcessAsync` |
| 3 | `GET` | `/v3/finance/split/statistics` | Recuperar valores de split | `GetAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Recuperar saldo da conta
/// </summary>
/// <see href="https://docs.asaas.com/reference/informacoes-financeiras">Documentação Oficial</see>
var result = await client.ListAsync();
```

---

## Informações fiscais

**Total de Endpoints**: 3

**Documentação Oficial**: [https://docs.asaas.com/reference/informacoes-fiscais](https://docs.asaas.com/reference/informacoes-fiscais)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `GET` | `/v3/customerFiscalInfo/municipalOptions` | Listar configurações municipais | `ListAsync` |
| 2 | `POST` | `/v3/customerFiscalInfo` | Criar e atualizar informações fiscais | `CreateAsync` |
| 3 | `GET` | `/v3/customerFiscalInfo` | Recuperar informações fiscais | `GetAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Listar configurações municipais
/// </summary>
/// <see href="https://docs.asaas.com/reference/informacoes-fiscais">Documentação Oficial</see>
var result = await client.ListAsync();
```

---

## Link de Pagamentos

**Total de Endpoints**: 11

**Documentação Oficial**: [https://docs.asaas.com/reference/link-de-pagamentos](https://docs.asaas.com/reference/link-de-pagamentos)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `POST` | `/v3/paymentLinks` | Criar um link de pagamentos | `CreateAsync` |
| 2 | `PUT` | `/v3/paymentLinks/{{id_do_link_de_pagamento}}` | Atualizar um link de pagamentos | `UpdateAsync` |
| 3 | `GET` | `/v3/paymentLinks/{{id_do_link_de_pagamento}}` | Recuperar um único link de pagamentos | `GetByIdAsync` |
| 4 | `GET` | `/v3/paymentLinks` | Listar link de pagamentos | `ListAsync` |
| 5 | `DELETE` | `/v3/paymentLinks/{{id_do_link_de_pagamento}}` | Remover um link de pagamentos | `DeleteAsync` |
| 6 | `POST` | `/v3/paymentLinks/{{id_do_link_de_pagamento}}/restore` | Restaurar um link de pagamentos | `RestoreAsync` |
| 7 | `POST` | `/v3/paymentLinks/{{id_do_link_de_pagamento}}/images` | Adicionar uma imagem a um link de pagamentos | `ProcessAsync` |
| 8 | `GET` | `/v3/paymentLinks/{{id_do_link_de_pagamento}}/images/{{id_da_imagem_do_link_de_pagamento}}` | Recuperar uma única imagem de um link de pagamentos | `GetByIdAsync` |
| 9 | `GET` | `/v3/paymentLinks/{{id_do_link_de_pagamento}}/images` | Listar imagens de um link de pagamentos | `ListAsync` |
| 10 | `DELETE` | `/v3/paymentLinks/{{id_do_link_de_pagamento}}/images` | Remover uma imagem do link de pagamentos | `DeleteAsync` |
| 11 | `POST` | `/v3/paymentLinks/{{id_do_link_de_pagamento}}/images/{{id_da_imagem_do_link_de_pagamento}}/setAsMain` | Definir imagem principal do link de pagamentos | `ProcessAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Criar um link de pagamentos
/// </summary>
/// <see href="https://docs.asaas.com/reference/link-de-pagamentos">Documentação Oficial</see>
var request = new Request();
var result = await client.CreateAsync(request);
```

---

## Negativações

**Total de Endpoints**: 9

**Documentação Oficial**: [https://docs.asaas.com/reference/negativacoes](https://docs.asaas.com/reference/negativacoes)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `POST` | `/v3/paymentDunnings` | Criar uma negativação | `CreateAsync` |
| 2 | `POST` | `/v3/paymentDunnings/simulate` | Simular uma negativação | `SimulateAsync` |
| 3 | `GET` | `/v3/paymentDunnings/{{id_da_recuperacao}}` | Recuperar uma única negativação | `GetByIdAsync` |
| 4 | `GET` | `/v3/paymentDunnings` | Listar negativações | `ListAsync` |
| 5 | `GET` | `/v3/paymentDunnings/{{id_da_recuperacao}}/history` | Listar histórico de eventos | `ListAsync` |
| 6 | `GET` | `/v3/paymentDunnings/{{id_da_recuperacao}}/partialPayments` | Listar pagamentos recebidos | `ListAsync` |
| 7 | `GET` | `/v3/paymentDunnings/paymentsAvailableForDunning` | Listar cobranças disponíveis para negativação | `ListAsync` |
| 8 | `POST` | `/v3/paymentDunnings/{{id_da_recuperacao}}/documents` | Reenviar documentos | `ProcessAsync` |
| 9 | `POST` | `/v3/paymentDunnings/{{id_da_recuperacao}}/cancel` | Cancelar negativação | `CancelAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Criar uma negativação
/// </summary>
/// <see href="https://docs.asaas.com/reference/negativacoes">Documentação Oficial</see>
var request = new Request();
var result = await client.CreateAsync(request);
```

---

## Notas fiscais

**Total de Endpoints**: 7

**Documentação Oficial**: [https://docs.asaas.com/reference/notas-fiscais](https://docs.asaas.com/reference/notas-fiscais)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `POST` | `/v3/invoices` | Agendar nota fiscal | `ProcessAsync` |
| 2 | `PUT` | `/v3/invoices/{{id_da_nota_fiscal}}` | Atualizar nota fiscal | `UpdateAsync` |
| 3 | `GET` | `/v3/invoices/{{id_da_nota_fiscal}}` | Recuperar uma nota fiscal | `GetByIdAsync` |
| 4 | `GET` | `/v3/invoices` | Listar notas fiscais | `ListAsync` |
| 5 | `POST` | `/v3/invoices/{{id_da_nota_fiscal}}/authorize` | Emitir uma nota fiscal | `AuthorizeAsync` |
| 6 | `POST` | `/v3/invoices/{{id_da_nota_fiscal}}/cancel` | Cancelar uma nota fiscal | `CancelAsync` |
| 7 | `GET` | `/v3/fiscalInfo/services` | Listar serviços municipais | `ListAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Agendar nota fiscal
/// </summary>
/// <see href="https://docs.asaas.com/reference/notas-fiscais">Documentação Oficial</see>
var request = new Request();
var result = await client.CreateAsync(request);
```

---

## Notificações

**Total de Endpoints**: 2

**Documentação Oficial**: [https://docs.asaas.com/reference/notificacoes](https://docs.asaas.com/reference/notificacoes)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `POST` | `/v3/notifications/{{id_da_notificacao}}` | Atualizar notificação existente | `UpdateAsync` |
| 2 | `POST` | `/v3/notifications/batch` | Atualizar notificação existente em lote | `UpdateAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Atualizar notificação existente
/// </summary>
/// <see href="https://docs.asaas.com/reference/notificacoes">Documentação Oficial</see>
var request = new Request();
var result = await client.CreateAsync(request);
```

---

## Pagamento de contas

**Total de Endpoints**: 5

**Documentação Oficial**: [https://docs.asaas.com/reference/pagamento-de-contas](https://docs.asaas.com/reference/pagamento-de-contas)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `POST` | `/v3/bill` | Criar um pagamento de conta | `CreateAsync` |
| 2 | `POST` | `/v3/bill/simulate` | Simular um pagamento de conta | `SimulateAsync` |
| 3 | `GET` | `/v3/bill/{{id_do_pagamento_de_conta}}` | Recuperar um único pagamento de conta | `GetByIdAsync` |
| 4 | `GET` | `/v3/bill` | Listar pagamentos de conta | `ListAsync` |
| 5 | `POST` | `/v3/bill/{{id_do_pagamento_de_conta}}/cancel` | Cancelar pagamento de conta | `CancelAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Criar um pagamento de conta
/// </summary>
/// <see href="https://docs.asaas.com/reference/pagamento-de-contas">Documentação Oficial</see>
var request = new Request();
var result = await client.CreateAsync(request);
```

---

## Parcelamentos

**Total de Endpoints**: 6

**Documentação Oficial**: [https://docs.asaas.com/reference/parcelamentos](https://docs.asaas.com/reference/parcelamentos)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `GET` | `/v3/installments/{{id_da_parcela}}` | Recuperar um único parcelamento | `GetByIdAsync` |
| 2 | `GET` | `/v3/installments` | Listar parcelamentos | `ListAsync` |
| 3 | `GET` | `/v3/installments/{{id_da_parcela}}/payments` | Listar cobranças de um parcelamento | `ListAsync` |
| 4 | `GET` | `/v3/installments/{{id_da_parcela}}/paymentBook` | Listar carnê de parcelamento | `ListAsync` |
| 5 | `DELETE` | `/v3/installments/{{id_da_parcela}}` | Remover parcelamento | `DeleteAsync` |
| 6 | `POST` | `/v3/installments/{{id_da_parcela}}/refund` | Estornar parcelamento | `RefundAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Recuperar um único parcelamento
/// </summary>
/// <see href="https://docs.asaas.com/reference/parcelamentos">Documentação Oficial</see>
var result = await client.GetByIdAsync(id);
```

---

## Pix

**Total de Endpoints**: 10

**Documentação Oficial**: [https://docs.asaas.com/reference/pix](https://docs.asaas.com/reference/pix)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `GET` | `/v3/pix/transactions` | Recuperar uma única transação | `GetAsync` |
| 2 | `GET` | `/v3/pix/transactions` | Listar transações | `ListAsync` |
| 3 | `POST` | `/v3/pix/transactions/{{id_da_transacao_pix}}/cancel` | Cancelar uma transação agendada | `CancelAsync` |
| 4 | `POST` | `/v3/pix/qrCodes/static` | Criar um QRCode estático | `CreateAsync` |
| 5 | `POST` | `/v3/pix/qrCodes/decode` | Decodificar um QRCode | `DecodeAsync` |
| 6 | `POST` | `/v3/pix/qrCodes/pay` | Pagar um QRCode | `ProcessAsync` |
| 7 | `POST` | `/v3/pix/addressKeys` | Criar uma chave | `CreateAsync` |
| 8 | `GET` | `/v3/pix/addressKeys` | Listar chaves | `ListAsync` |
| 9 | `GET` | `/v3/pix/addressKeys/{{id_da_chave_pix}}` | Recuperar uma única chave | `GetByIdAsync` |
| 10 | `DELETE` | `/v3/pix/addressKeys/{{id_da_chave_pix}}` | Remover chave | `DeleteAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Recuperar uma única transação
/// </summary>
/// <see href="https://docs.asaas.com/reference/pix">Documentação Oficial</see>
var result = await client.ListAsync();
```

---

## Transferências

**Total de Endpoints**: 7

**Documentação Oficial**: [https://docs.asaas.com/reference/transferencias](https://docs.asaas.com/reference/transferencias)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `POST` | `/v3/transfers` | Transferir para conta bancária via TED | `ProcessAsync` |
| 2 | `POST` | `/v3/transfers` | Transferir para conta bancária via Pix manual | `ProcessAsync` |
| 3 | `POST` | `/v3/transfers` | Transferir via Pix com Chave | `ProcessAsync` |
| 4 | `POST` | `/v3/transfers` | Transferir para conta bancária via Pix manual (agendado) | `ProcessAsync` |
| 5 | `POST` | `/v3/transfers` | Transferir para conta ASAAS | `ProcessAsync` |
| 6 | `GET` | `/v3/transfers/{{id_da_transferencia}}` | Recuperar uma única transferência | `GetByIdAsync` |
| 7 | `GET` | `/v3/transfers` | Listar transferências | `ListAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Transferir para conta bancária via TED
/// </summary>
/// <see href="https://docs.asaas.com/reference/transferencias">Documentação Oficial</see>
var request = new Request();
var result = await client.CreateAsync(request);
```

---

## Webhook de Cobrança

**Total de Endpoints**: 2

**Documentação Oficial**: [https://docs.asaas.com/reference/webhooks](https://docs.asaas.com/reference/webhooks)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `POST` | `/v3/webhook` | Criar ou atualizar configurações para Webhook | `CreateAsync` |
| 2 | `GET` | `/v3/webhook` | Recuperar configurações de webhook | `GetAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Criar ou atualizar configurações para Webhook
/// </summary>
/// <see href="https://docs.asaas.com/reference/webhooks">Documentação Oficial</see>
var request = new Request();
var result = await client.CreateAsync(request);
```

---

## Webhook de Notas Fiscais

**Total de Endpoints**: 2

**Documentação Oficial**: [https://docs.asaas.com/reference/webhooks-de-notas-fiscais](https://docs.asaas.com/reference/webhooks-de-notas-fiscais)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `POST` | `/v3/webhook/invoice` | Criar ou atualizar configurações para webhook | `CreateAsync` |
| 2 | `GET` | `/v3/webhook/invoice` | Recuperar configuração de webhook | `GetAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Criar ou atualizar configurações para webhook
/// </summary>
/// <see href="https://docs.asaas.com/reference/webhooks-de-notas-fiscais">Documentação Oficial</see>
var request = new Request();
var result = await client.CreateAsync(request);
```

---

## Webhook de Recarga de celular

**Total de Endpoints**: 2

**Documentação Oficial**: [https://docs.asaas.com/reference/webhooks-de-recarga-de-celular](https://docs.asaas.com/reference/webhooks-de-recarga-de-celular)

| # | Método | Endpoint | Nome | Cliente/Método |
|---|--------|----------|------|----------------|
| 1 | `POST` | `/v3/webhook/mobilePhoneRecharge` | Criar ou atualizar configurações para webhook | `CreateAsync` |
| 2 | `GET` | `/v3/webhook/mobilePhoneRecharge` | Recuperar configuração de webhook | `GetAsync` |

### Exemplo de Uso

```csharp
/// <summary>
/// Criar ou atualizar configurações para webhook
/// </summary>
/// <see href="https://docs.asaas.com/reference/webhooks-de-recarga-de-celular">Documentação Oficial</see>
var request = new Request();
var result = await client.CreateAsync(request);
```

---

