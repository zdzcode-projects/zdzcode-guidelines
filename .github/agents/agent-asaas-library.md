````markdown name=AGENT_INSTRUCTIONS.md
---
name: Asaas Library
description: Biblioteca C# para integração completa com a API do Asaas, seguindo padrões ZDZCode, com cobertura de testes unitários, exemplos de uso, CI, empacotamento e documentação.
---

# My Agent

Você deve implementar todos os endpoints disponibilizados pela API do Asaas. Utilize a collection oficial do Postman como fonte da verdade para listar e validar a cobertura dos endpoints:

Coleção Postman do Asaas:
https://raw.githubusercontent.com/asaasdev/postman-collection/main/asaasPostmanCollection.json

A entrega deve seguir as convenções de projeto da ZDZCode, incluir testes unitários abrangentes, exemplos de uso e pipelines de CI prontos, com qualidade de código e documentação em nível de produção.

--------------------------------------------------------------------

## 1) Objetivos e critérios de sucesso

- Cobertura 100% dos endpoints da coleção do Asaas (todas as operações definidas na coleção).
- Publicar um cliente idiomático em C# (.NET 8+) com:
  - Tipos fortes para requests/responses.
  - Tratamento de erros robusto e padronizado.
  - Paginação, filtros e parâmetros modelados de forma fluente.
  - Resiliência (timeouts, retries, backoff, circuit breaker).
  - Injeção de dependência nativa e integração com Microsoft.Extensions.*
  - Logging e telemetria mínimos, sem vazar dados sensíveis.
- Testes unitários com alta cobertura (mín. 90% linhas/branches) e cenários críticos:
  - Sucesso, validação, erros conhecidos, timeouts, 429/rate limit, 5xx/retry, serialização e paginação.
- Exemplo de uso (samples) pronto para desenvolvedores.
- Pipeline CI (GitHub Actions) rodando build, lint/format, testes e cobertura.
- Pronto para empacotamento NuGet (versão, metadata e README).
- Documentação clara de instalação, configuração, uso e extensibilidade.
- Verificação automática da cobertura dos endpoints comparando com a coleção Postman.

--------------------------------------------------------------------

## 2) Arquitetura e padrões (estilo ZDZCode)

- Versão do .NET: .NET 8.
- Convenções de pasta:
  - src/ZDZCode.Payments.Asaas/ (biblioteca principal)
  - tests/ZDZCode.Payments.Asaas.Tests/ (testes unitários)
  - samples/ZDZCode.Payments.Asaas.Samples/ (exemplos de uso)
  - tools/Asaas.Collection.Tools/ (scripts utilitários p/ cobertura de endpoints)
  - docs/ (README, CHANGELOG, guias)
- Convenções gerais:
  - Nullable enable (csproj): <Nullable>enable</Nullable>
  - Analisadores: Microsoft.CodeAnalysis.NetAnalyzers + StyleCop (com regra para documentação pública)
  - System.Text.Json como serializador padrão (camelCase; números e datas estritas; converters onde necessário)
  - HttpClient com HttpClientFactory (AddHttpClient) e Polly para resiliência
  - Opções via IOptions<AsaasOptions> com validação (DataAnnotations)
  - DI-first: métodos de extensão AddAsaasClient(...) e AddAsaas(...) para registrar todos os serviços
  - Interfaces para módulos/endpoints, p.ex. ICustomersClient, IPaymentsClient etc.

--------------------------------------------------------------------

## 3) Estrutura sugerida de código

- Configuration
  - AsaasOptions (BaseUrl, ApiKey, Timeout, RetryPolicy, UserAgent, UseSandbox etc.)
  - AsaasEndpoints (constantes/paths; gerado pelo parser da coleção)
- Http
  - IAsaasHttpClient (abstração mínima)
  - AsaasHttpClient (implementação; injeta HttpClient, headers padrão, serialização, error handling)
  - Resilience: Polly policies (retry com jitter, timeout, circuit breaker opcional)
- Errors
  - AsaasErrorResponse (modelo baseado nos erros da API)
  - AsaasApiException : Exception (inclui StatusCode, corpo, códigos de erro)
- Pagination
  - PageRequest (limit, offset ou page/size – seguir exatamente a semântica da API)
  - Page<T> e helpers (HasMore, NextPage)
  - Iteradores assíncronos IAsyncEnumerable<T> para varrer todas as páginas
- Módulos por endpoint (um por “folder/tag” da coleção)
  - Abstrações: ICustomersClient, IPaymentsClient, ISubscriptionsClient, IWebhooksClient, etc.
  - Implementações: CustomersClient, PaymentsClient, ...
  - Models: Requests/Responses específicos de cada endpoint
  - Cada método mapeia 1:1 os endpoints HTTP (rota, verbo, query/body)
- Webhooks
  - Modelos de eventos
  - Validadores/Helpers (ex.: validação de assinatura, se aplicável — ler coleção/Docs)
- Extensões
  - ServiceCollectionExtensions (AddAsaas, AddAsaasClient)
  - HttpClientBuilderExtensions (AddResiliencePolicies)

Observação importante: Não “chutar” nomes de headers/parametrizações. Sempre derivar da coleção oficial. Se a coleção trouxer exemplos/headers (ex.: auth), replicar fielmente.

--------------------------------------------------------------------

## 4) Autenticação e base URL

- API Key: ler o header/forma de autenticação diretamente da coleção do Postman e aplicar.
  - Usar DelegatingHandler para injetar o header de auth em todas as requisições.
- Base URL:
  - Definir AsaasOptions.BaseUrl com padrão para o ambiente de produção do Asaas (conforme coleção).
  - Se a coleção trouxer sandbox, suportar UseSandbox/AlternateBaseUrl.

--------------------------------------------------------------------

## 5) Serialização e validações

- System.Text.Json:
  - PropertyNamingPolicy = JsonNamingPolicy.CamelCase
  - IgnoreNullValues = true (ou DefaultIgnoreCondition = WhenWritingNull)
  - Converters específicos para DateOnly/TimeOnly, enums como strings, decimal invariante
- Validar AsaasOptions com IValidateOptions, garantindo:
  - ApiKey não vazia
  - BaseUrl válida (https)
  - Timeout > 0
- Validar requests (DataAnnotations/FluentValidation) quando aplicável antes de enviar.

--------------------------------------------------------------------

## 6) Paginação, filtros e consulta

- Implementar exatamente o mecanismo de paginação indicado pela coleção (limit/offset, page/size, etc.).
- Expor:
  - Métodos simples: Task<Page<T>> ListAsync(PageRequest request, CancellationToken ct)
  - Iteradores utilitários: IAsyncEnumerable<T> ListAllAsync(...), que cuidam da paginação internamente
- Modelar filtros fortemente tipados sempre que possível; quando genéricos, expor sobrecarga com dicionário/QueryBuilder.

--------------------------------------------------------------------

## 7) Resiliência, tempo limite e rate limiting

- Timeouts:
  - HttpClient.Timeout via AsaasOptions.Timeout
  - Policy de timeout adicional por operação (Polly)
- Retries (Polly):
  - Repetir em falhas transitórias: 408, 429, 5xx
  - Backoff exponencial com jitter
  - Respeitar Retry-After quando presente
- Circuit Breaker (opcional, desabilitado por padrão via opções)
- Tratar 429 (rate limit) de forma explícita:
  - Respeitar headers de rate-limit da API se fornecidos
  - Surfacing de AsaasApiException com metadados úteis

--------------------------------------------------------------------

## 8) Tratamento de erros e diagnósticos

- Para status >= 400:
  - Tentar desserializar o payload de erro do Asaas (seguir o formato da coleção)
  - Lançar AsaasApiException com StatusCode, ErrorCode(s), Mensagem, RawBody
- Logging (ILogger):
  - Logar RequestId/CorrelationId se houver, StatusCode, rota
  - Não logar dados sensíveis (ApiKey, tokens, dados PII)
- Opcional: ActivitySource/Tracing para integração com OpenTelemetry

--------------------------------------------------------------------

## 9) Descoberta automática de endpoints a partir da coleção

Criar uma ferramenta em tools/Asaas.Collection.Tools que:

- Baixa e interpreta a coleção Postman (JSON).
- Lista TODOS os endpoints (método + path + nome).
- Gera um artefato de verificação (ex.: tools/.cache/asaas-endpoints.json).
- Gera uma checklist/relatório de cobertura comparando:
  - Endpoints presentes na coleção
  - Métodos implementados na biblioteca (via convenção: interface + implementação + teste)
- Falha o build (ou o job de CI) quando cobertura < 100%.

Regras de mapeamento sugeridas:
- Para cada item da coleção (request):
  - Determinar “módulo” a partir da pasta/tag
  - Nome do método a partir do “name” do request (normalizado)
  - Path/params/headers/body diretamente do JSON

--------------------------------------------------------------------

## 10) Testes unitários (obrigatório)

- Framework: xUnit
- Mock de HTTP:
  - Preferir mocking do HttpMessageHandler (ex.: via Moq) OU RichardSzalay.MockHttp
  - Testar:
    - Cabeçalhos obrigatórios (incluindo auth)
    - Rota e query string corretas
    - Corpo serializado corretamente (incl. enums, datas, decimais)
    - Respostas 2xx (mapeamento em modelos)
    - Erros 4xx/5xx (AsaasApiException)
    - 429 com Retry-After (retry/backoff)
    - Timeouts e cancelamentos
- Paginação:
  - Cenários com múltiplas páginas (ListAllAsync)
- Serialização:
  - Testes de ida/volta (golden files) se a coleção trouxer exemplos
- Opções:
  - Validação de AsaasOptions (casos inválidos)
- Cobertura:
  - Alvo >= 90% linhas/branches
  - Expor relatório cobertura (Coverlet) e publicar no CI

Sugestão de estrutura de testes:
- Http/AsaasHttpClientTests.cs
- Errors/AsaasApiExceptionTests.cs
- Modules/Customers/CustomersClientTests.cs
- Modules/Payments/PaymentsClientTests.cs
- ... (um por módulo/endpoint)
- Pagination/PaginationTests.cs
- Options/AsaasOptionsValidationTests.cs

--------------------------------------------------------------------

## 11) Exemplos (samples)

Criar samples mínimos mostrando:

- Registro no DI:
  - builder.Services.AddAsaas(options => { options.ApiKey = "..."; options.UseSandbox = true; ... });
- Uso de 2–3 endpoints comuns (criando cliente via ICustomersClient, IPaymentsClient, etc.)
- Paginação (ListAllAsync)
- Tratamento de erro com try/catch AsaasApiException

--------------------------------------------------------------------

## 12) CI/CD e qualidade

- GitHub Actions workflow:
  - dotnet restore/build/test
  - dotnet format --verify-no-changes
  - Analisadores (warnings as errors em Release)
  - Testes com cobertura (Coverlet + report)
  - Job que executa a ferramenta de cobertura de endpoints (tools/Asaas.Collection.Tools)
- Opcional: Geração de pacote NuGet no push de tag vX.Y.Z
  - IncludeSymbols, SourceLink
  - PackageReadmeFile (docs/README.NuGet.md)
  - Metadata (Authors, RepositoryUrl, License, Tags)

--------------------------------------------------------------------

## 13) Documentação

- README principal com:
  - Instalação via NuGet
  - Configuração (AsaasOptions)
  - Autenticação
  - Exemplos de uso
  - Paginação
  - Tratamento de erros
  - Resiliência
  - Webhooks
- CHANGELOG (Keep a Changelog)
- CONTRIBUTING (padrões, estilo, como rodar testes e ferramentas)
- ROADMAP (se aplicável)
- Referência de módulos/endpoints implementados (gerada a partir da ferramenta de cobertura)

--------------------------------------------------------------------

## 14) Versionamento e compatibilidade

- Seguir SemVer (ex.: 1.0.0 para primeira versão estável).
- Se a API do Asaas tiver versionamento por path (ex.: /v3), refletir em AsaasOptions.BaseUrl.
- Quebra de compatibilidade da API deve resultar em major bump.

--------------------------------------------------------------------

## 15) Definição de Pronto (DoD)

- [ ] 100% dos endpoints da coleção cobertos por implementações e testes unitários.
- [ ] Ferramenta de verificação de cobertura de endpoints passando no CI.
- [ ] Cobertura de testes >= 90%.
- [ ] Pacote buildável em Release, analisadores sem warnings, format limpo.
- [ ] Samples funcionais.
- [ ] Documentação completa (README, CHANGELOG).
- [ ] Publicação (local) de pacote NuGet validada.

--------------------------------------------------------------------

## 16) Passo a passo resumido para execução

1) Bootstrapping do projeto:
- Criar soluções e projetos nas pastas indicadas.
- Habilitar nullable, analisadores e StyleCop.
- Adicionar pacotes: Microsoft.Extensions.Http, Polly.Extensions.Http, System.Text.Json, Moq/xUnit/MockHttp, Coverlet, StyleCop.Analyzers.

2) Implementar infraestrutura:
- AsaasOptions + validações.
- ServiceCollectionExtensions (AddAsaas).
- AsaasHttpClient com headers, serialização, error handling e resiliência.

3) Ferramenta de coleção:
- Baixar e parsear a coleção Postman.
- Gerar lista de endpoints e relatório de cobertura.
- Wire no CI para falhar se cobertura < 100%.

4) Implementar módulos por prioridade (guiado pela coleção):
- Para cada pasta/tag: criar interface, implementação e modelos.
- Adicionar testes unitários completos para cada método.
- Atualizar relatório de cobertura até 100%.

5) Samples e docs:
- Produzir exemplos reais.
- Escrever README e CHANGELOG.

6) Pipeline:
- Criar workflow CI e validar.
- Opcional: pipeline de release para NuGet.

--------------------------------------------------------------------

## 17) Observações importantes

- Não introduzir “criatividade” no protocolo: sempre seguir exatamente método/rota/headers/params do Postman.
- Onde a coleção trouxer exemplos de payloads/erros, criar testes com “golden files” para garantir compatibilidade.
- Tratar dados sensíveis com cuidado nos logs.
- Priorizar interfaces e DI para facilitar mocking e testes.
- Se a API suportar idempotência (ex.: cabeçalho de idempotency-key), implementar via opções/handler.
- Se houver Webhooks com assinatura, implementar verificação de assinatura lendo a especificação/coleção.

--------------------------------------------------------------------

## 18) Entregáveis finais

- Código em src/ com todos os módulos implementados.
- Testes unitários em tests/ com cobertura reportada.
- Ferramenta em tools/ para verificação de cobertura da coleção.
- Samples executáveis.
- CI configurado e passando.
- Documentação completa.
````
