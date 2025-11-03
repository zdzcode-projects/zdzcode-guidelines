# Guia de Implementação para Agentes de IA

Este documento fornece instruções detalhadas para agentes de IA implementarem funcionalidades seguindo os padrões e estrutura de código da **ZDZCode**. Utilize este guia ao criar endpoints, testes unitários, entidades e componentes frontend.

## 📋 Índice

1. [Visão Geral da Arquitetura](#visão-geral-da-arquitetura)
2. [Implementação de Endpoints API](#implementação-de-endpoints-api)
3. [Implementação de Testes Unitários](#implementação-de-testes-unitários)
4. [Criação de Entidades](#criação-de-entidades)
5. [Implementação Frontend](#implementação-frontend)
6. [Padrões de Código](#padrões-de-código)
7. [Integração e CI/CD](#integração-e-cicd)

---

## 🏗️ Visão Geral da Arquitetura

### Stack Tecnológica

- **Backend**: ASP.NET Core 8/9 (.NET 8/9)
- **Frontend**: Vue 3 + Nuxt 3 + Vuetify 3
- **Banco de Dados**: SQL Server (on-premises e Azure SQL Database)
- **Testes**: xUnit ou NUnit (backend), Vitest/Jest (frontend)
- **CI/CD**: Azure DevOps Pipelines
- **Controle de Versão**: Azure Repos (Git)

### Estrutura de Projeto Backend (Clean Architecture)

```
src/
├── Domain/                    # Entidades, Value Objects, Enums, Interfaces de domínio
│   ├── Entities/
│   ├── Enums/
│   ├── Interfaces/
│   └── ValueObjects/
├── Application/              # Casos de uso, DTOs, Interfaces de serviços
│   ├── DTOs/
│   ├── Interfaces/
│   ├── Services/
│   └── Validators/
├── Infrastructure/           # Implementações de acesso a dados, serviços externos
│   ├── Data/
│   │   ├── Contexts/
│   │   ├── Configurations/
│   │   └── Repositories/
│   └── Services/
└── API/                      # Controllers, Middlewares, Configurações
    ├── Controllers/
    ├── Middlewares/
    └── Extensions/
```

---

## 🔌 Implementação de Endpoints API

### Passo 1: Definir a Entidade no Domain

**Localização**: `src/Domain/Entities/`

```csharp
using ZDZCode.Data.EntityFramework;

namespace [Projeto].Domain.Entities
{
    /// <summary>
    /// Representa um pedido no sistema
    /// </summary>
    public class Pedido : BaseEntity
    {
        public DateTime CriadoEm { get; private set; }
        public DateTime? AtualizadoEm { get; private set; }
        public StatusPedido Status { get; private set; }
        public decimal ValorTotal { get; private set; }
        public Guid ClienteId { get; private set; }
        
        // Propriedades de navegação
        public Cliente Cliente { get; private set; }
        public ICollection<ItemPedido> Itens { get; private set; } = new List<ItemPedido>();

        // Construtor privado para EF
        private Pedido() { }

        // Construtor com validações
        public Pedido(Guid clienteId, decimal valorTotal)
        {
            Id = Guid.NewGuid();
            ClienteId = clienteId;
            ValorTotal = valorTotal;
            Status = StatusPedido.Pendente;
            CriadoEm = DateTime.UtcNow;
        }

        // Métodos de negócio
        public void AtualizarStatus(StatusPedido novoStatus)
        {
            Status = novoStatus;
            AtualizadoEm = DateTime.UtcNow;
        }
    }
}
```

### Passo 2: Criar o Enum (se necessário)

**Localização**: `src/Domain/Enums/`

```csharp
namespace [Projeto].Domain.Enums
{
    public enum StatusPedido
    {
        Pendente = 0,
        Confirmado = 1,
        EmProcessamento = 2,
        Enviado = 3,
        Entregue = 4,
        Cancelado = 5
    }
}
```

### Passo 3: Configurar Mapeamento com Entity Framework

**Localização**: `src/Infrastructure/Data/Configurations/`

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Metadata.Builders;
using [Projeto].Domain.Entities;

namespace [Projeto].Infrastructure.Data.Configurations
{
    public class PedidoConfiguration : IEntityTypeConfiguration<Pedido>
    {
        public void Configure(EntityTypeBuilder<Pedido> builder)
        {
            builder.ToTable("Pedidos");
            
            builder.HasKey(p => p.Id);
            
            builder.Property(p => p.CriadoEm)
                .IsRequired()
                .HasColumnType("datetime2");
            
            builder.Property(p => p.AtualizadoEm)
                .HasColumnType("datetime2");
            
            builder.Property(p => p.Status)
                .IsRequired()
                .HasConversion<int>();
            
            builder.Property(p => p.ValorTotal)
                .IsRequired()
                .HasColumnType("decimal(18,2)");
            
            builder.Property(p => p.ClienteId)
                .IsRequired();

            // Relacionamentos
            builder.HasOne(p => p.Cliente)
                .WithMany()
                .HasForeignKey(p => p.ClienteId)
                .OnDelete(DeleteBehavior.Restrict);

            builder.HasMany(p => p.Itens)
                .WithOne()
                .HasForeignKey("PedidoId")
                .OnDelete(DeleteBehavior.Cascade);
        }
    }
}
```

### Passo 4: Criar DTOs na Application

**Localização**: `src/Application/DTOs/`

```csharp
namespace [Projeto].Application.DTOs
{
    /// <summary>
    /// DTO para criação de pedido
    /// </summary>
    public class CriarPedidoDto
    {
        public Guid ClienteId { get; set; }
        public decimal ValorTotal { get; set; }
        public List<ItemPedidoDto> Itens { get; set; } = new();
    }

    /// <summary>
    /// DTO de resposta de pedido
    /// </summary>
    public class PedidoResponseDto
    {
        public Guid Id { get; set; }
        public DateTime CriadoEm { get; set; }
        public DateTime? AtualizadoEm { get; set; }
        public string Status { get; set; }
        public decimal ValorTotal { get; set; }
        public Guid ClienteId { get; set; }
        public List<ItemPedidoDto> Itens { get; set; }
    }
}
```

### Passo 5: Criar Interface do Repositório

**Localização**: `src/Domain/Interfaces/`

```csharp
namespace [Projeto].Domain.Interfaces
{
    public interface IRepositorioPedidos
    {
        Task<Pedido> ObterPorIdAsync(Guid id);
        Task<IEnumerable<Pedido>> ListarTodosAsync();
        Task<Pedido> AdicionarAsync(Pedido pedido);
        Task AtualizarAsync(Pedido pedido);
        Task RemoverAsync(Guid id);
    }
}
```

### Passo 6: Implementar Repositório

**Localização**: `src/Infrastructure/Data/Repositories/`

```csharp
using Microsoft.EntityFrameworkCore;
using [Projeto].Domain.Entities;
using [Projeto].Domain.Interfaces;

namespace [Projeto].Infrastructure.Data.Repositories
{
    public class RepositorioPedidos : IRepositorioPedidos
    {
        private readonly AppDbContext _context;

        public RepositorioPedidos(AppDbContext context)
        {
            _context = context;
        }

        public async Task<Pedido> ObterPorIdAsync(Guid id)
        {
            return await _context.Pedidos
                .Include(p => p.Itens)
                .Include(p => p.Cliente)
                .FirstOrDefaultAsync(p => p.Id == id);
        }

        public async Task<IEnumerable<Pedido>> ListarTodosAsync()
        {
            return await _context.Pedidos
                .Include(p => p.Itens)
                .Include(p => p.Cliente)
                .ToListAsync();
        }

        public async Task<Pedido> AdicionarAsync(Pedido pedido)
        {
            await _context.Pedidos.AddAsync(pedido);
            await _context.SaveChangesAsync();
            return pedido;
        }

        public async Task AtualizarAsync(Pedido pedido)
        {
            var pedidoExistente = await ObterPorIdAsync(pedido.Id);
            if (pedidoExistente == null)
                throw new InvalidOperationException($"Pedido com ID {pedido.Id} não encontrado");

            _context.Pedidos.Update(pedido);
            await _context.SaveChangesAsync();
        }

        public async Task RemoverAsync(Guid id)
        {
            var pedido = await ObterPorIdAsync(id);
            if (pedido != null)
            {
                _context.Pedidos.Remove(pedido);
                await _context.SaveChangesAsync();
            }
        }
    }
}
```

### Passo 7: Criar Serviço de Aplicação

**Localização**: `src/Application/Services/`

```csharp
using [Projeto].Application.DTOs;
using [Projeto].Domain.Entities;
using [Projeto].Domain.Interfaces;

namespace [Projeto].Application.Services
{
    public interface IPedidoService
    {
        Task<PedidoResponseDto> CriarPedidoAsync(CriarPedidoDto dto);
        Task<PedidoResponseDto> ObterPedidoPorIdAsync(Guid id);
        Task<IEnumerable<PedidoResponseDto>> ListarPedidosAsync();
    }

    public class PedidoService : IPedidoService
    {
        private readonly IRepositorioPedidos _repositorioPedidos;

        public PedidoService(IRepositorioPedidos repositorioPedidos)
        {
            _repositorioPedidos = repositorioPedidos;
        }

        public async Task<PedidoResponseDto> CriarPedidoAsync(CriarPedidoDto dto)
        {
            var pedido = new Pedido(dto.ClienteId, dto.ValorTotal);
            
            var pedidoCriado = await _repositorioPedidos.AdicionarAsync(pedido);
            
            return MapearParaDto(pedidoCriado);
        }

        public async Task<PedidoResponseDto> ObterPedidoPorIdAsync(Guid id)
        {
            var pedido = await _repositorioPedidos.ObterPorIdAsync(id);
            return pedido != null ? MapearParaDto(pedido) : null;
        }

        public async Task<IEnumerable<PedidoResponseDto>> ListarPedidosAsync()
        {
            var pedidos = await _repositorioPedidos.ListarTodosAsync();
            return pedidos.Select(MapearParaDto);
        }

        private PedidoResponseDto MapearParaDto(Pedido pedido)
        {
            return new PedidoResponseDto
            {
                Id = pedido.Id,
                CriadoEm = pedido.CriadoEm,
                AtualizadoEm = pedido.AtualizadoEm,
                Status = pedido.Status.ToString(),
                ValorTotal = pedido.ValorTotal,
                ClienteId = pedido.ClienteId
            };
        }
    }
}
```

### Passo 8: Criar Controller

**Localização**: `src/API/Controllers/`

```csharp
using Microsoft.AspNetCore.Mvc;
using [Projeto].Application.DTOs;
using [Projeto].Application.Services;

namespace [Projeto].API.Controllers
{
    [ApiController]
    [Route("api/[controller]")]
    public class PedidosController : ControllerBase
    {
        private readonly IPedidoService _pedidoService;
        private readonly ILogger<PedidosController> _logger;

        public PedidosController(IPedidoService pedidoService, ILogger<PedidosController> logger)
        {
            _pedidoService = pedidoService;
            _logger = logger;
        }

        /// <summary>
        /// Lista todos os pedidos
        /// </summary>
        /// <returns>Lista de pedidos</returns>
        [HttpGet]
        [ProducesResponseType(typeof(IEnumerable<PedidoResponseDto>), StatusCodes.Status200OK)]
        public async Task<ActionResult<IEnumerable<PedidoResponseDto>>> ListarPedidos()
        {
            try
            {
                var pedidos = await _pedidoService.ListarPedidosAsync();
                return Ok(pedidos);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Erro ao listar pedidos");
                return StatusCode(500, "Erro interno ao processar requisição");
            }
        }

        /// <summary>
        /// Obtém um pedido por ID
        /// </summary>
        /// <param name="id">ID do pedido</param>
        /// <returns>Dados do pedido</returns>
        [HttpGet("{id}")]
        [ProducesResponseType(typeof(PedidoResponseDto), StatusCodes.Status200OK)]
        [ProducesResponseType(StatusCodes.Status404NotFound)]
        public async Task<ActionResult<PedidoResponseDto>> ObterPedido(Guid id)
        {
            try
            {
                var pedido = await _pedidoService.ObterPedidoPorIdAsync(id);
                
                if (pedido == null)
                    return NotFound($"Pedido com ID {id} não encontrado");
                
                return Ok(pedido);
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Erro ao obter pedido {PedidoId}", id);
                return StatusCode(500, "Erro interno ao processar requisição");
            }
        }

        /// <summary>
        /// Cria um novo pedido
        /// </summary>
        /// <param name="dto">Dados do pedido a ser criado</param>
        /// <returns>Pedido criado</returns>
        [HttpPost]
        [ProducesResponseType(typeof(PedidoResponseDto), StatusCodes.Status201Created)]
        [ProducesResponseType(StatusCodes.Status400BadRequest)]
        public async Task<ActionResult<PedidoResponseDto>> CriarPedido([FromBody] CriarPedidoDto dto)
        {
            try
            {
                if (!ModelState.IsValid)
                    return BadRequest(ModelState);

                var pedido = await _pedidoService.CriarPedidoAsync(dto);
                
                return CreatedAtAction(
                    nameof(ObterPedido),
                    new { id = pedido.Id },
                    pedido
                );
            }
            catch (Exception ex)
            {
                _logger.LogError(ex, "Erro ao criar pedido");
                return StatusCode(500, "Erro interno ao processar requisição");
            }
        }
    }
}
```

### Passo 9: Registrar Dependências

**Localização**: `src/API/Extensions/ServiceCollectionExtensions.cs`

```csharp
using [Projeto].Application.Services;
using [Projeto].Domain.Interfaces;
using [Projeto].Infrastructure.Data.Repositories;

namespace [Projeto].API.Extensions
{
    public static class ServiceCollectionExtensions
    {
        public static IServiceCollection AddApplicationServices(this IServiceCollection services)
        {
            // Repositórios
            services.AddScoped<IRepositorioPedidos, RepositorioPedidos>();
            
            // Serviços
            services.AddScoped<IPedidoService, PedidoService>();
            
            return services;
        }
    }
}
```

---

## 🧪 Implementação de Testes Unitários

### Estrutura de Projeto de Testes

```
tests/
├── [Projeto].Domain.Tests/
├── [Projeto].Application.Tests/
├── [Projeto].Infrastructure.Tests/
└── [Projeto].API.Tests/
```

### Teste de Entidade (Domain)

**Localização**: `tests/[Projeto].Domain.Tests/Entities/`

```csharp
using Xunit;
using [Projeto].Domain.Entities;
using [Projeto].Domain.Enums;

namespace [Projeto].Domain.Tests.Entities
{
    public class PedidoTests
    {
        [Fact]
        public void Pedido_DeveCriarComValoresCorretos()
        {
            // Arrange
            var clienteId = Guid.NewGuid();
            var valorTotal = 100.50m;

            // Act
            var pedido = new Pedido(clienteId, valorTotal);

            // Assert
            Assert.NotEqual(Guid.Empty, pedido.Id);
            Assert.Equal(clienteId, pedido.ClienteId);
            Assert.Equal(valorTotal, pedido.ValorTotal);
            Assert.Equal(StatusPedido.Pendente, pedido.Status);
            Assert.True(pedido.CriadoEm <= DateTime.UtcNow);
        }

        [Fact]
        public void AtualizarStatus_DeveAlterarStatusEDataAtualizacao()
        {
            // Arrange
            var pedido = new Pedido(Guid.NewGuid(), 100m);
            var statusAnterior = pedido.Status;
            var novoStatus = StatusPedido.Confirmado;

            // Act
            pedido.AtualizarStatus(novoStatus);

            // Assert
            Assert.NotEqual(statusAnterior, pedido.Status);
            Assert.Equal(novoStatus, pedido.Status);
            Assert.NotNull(pedido.AtualizadoEm);
        }
    }
}
```

### Teste de Serviço (Application)

**Localização**: `tests/[Projeto].Application.Tests/Services/`

```csharp
using Moq;
using Xunit;
using [Projeto].Application.DTOs;
using [Projeto].Application.Services;
using [Projeto].Domain.Entities;
using [Projeto].Domain.Interfaces;

namespace [Projeto].Application.Tests.Services
{
    public class PedidoServiceTests
    {
        private readonly Mock<IRepositorioPedidos> _mockRepositorio;
        private readonly PedidoService _service;

        public PedidoServiceTests()
        {
            _mockRepositorio = new Mock<IRepositorioPedidos>();
            _service = new PedidoService(_mockRepositorio.Object);
        }

        [Fact]
        public async Task CriarPedidoAsync_DeveRetornarPedidoCriado()
        {
            // Arrange
            var dto = new CriarPedidoDto
            {
                ClienteId = Guid.NewGuid(),
                ValorTotal = 150.00m
            };

            var pedidoEsperado = new Pedido(dto.ClienteId, dto.ValorTotal);
            
            _mockRepositorio
                .Setup(r => r.AdicionarAsync(It.IsAny<Pedido>()))
                .ReturnsAsync(pedidoEsperado);

            // Act
            var resultado = await _service.CriarPedidoAsync(dto);

            // Assert
            Assert.NotNull(resultado);
            Assert.Equal(dto.ClienteId, resultado.ClienteId);
            Assert.Equal(dto.ValorTotal, resultado.ValorTotal);
            _mockRepositorio.Verify(r => r.AdicionarAsync(It.IsAny<Pedido>()), Times.Once);
        }

        [Fact]
        public async Task ObterPedidoPorIdAsync_PedidoExistente_DeveRetornarPedido()
        {
            // Arrange
            var pedidoId = Guid.NewGuid();
            var pedido = new Pedido(Guid.NewGuid(), 100m);
            
            _mockRepositorio
                .Setup(r => r.ObterPorIdAsync(pedidoId))
                .ReturnsAsync(pedido);

            // Act
            var resultado = await _service.ObterPedidoPorIdAsync(pedidoId);

            // Assert
            Assert.NotNull(resultado);
            _mockRepositorio.Verify(r => r.ObterPorIdAsync(pedidoId), Times.Once);
        }

        [Fact]
        public async Task ObterPedidoPorIdAsync_PedidoInexistente_DeveRetornarNull()
        {
            // Arrange
            var pedidoId = Guid.NewGuid();
            
            _mockRepositorio
                .Setup(r => r.ObterPorIdAsync(pedidoId))
                .ReturnsAsync((Pedido)null);

            // Act
            var resultado = await _service.ObterPedidoPorIdAsync(pedidoId);

            // Assert
            Assert.Null(resultado);
        }
    }
}
```

### Teste de Controller (API)

**Localização**: `tests/[Projeto].API.Tests/Controllers/`

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Moq;
using Xunit;
using [Projeto].API.Controllers;
using [Projeto].Application.DTOs;
using [Projeto].Application.Services;

namespace [Projeto].API.Tests.Controllers
{
    public class PedidosControllerTests
    {
        private readonly Mock<IPedidoService> _mockService;
        private readonly Mock<ILogger<PedidosController>> _mockLogger;
        private readonly PedidosController _controller;

        public PedidosControllerTests()
        {
            _mockService = new Mock<IPedidoService>();
            _mockLogger = new Mock<ILogger<PedidosController>>();
            _controller = new PedidosController(_mockService.Object, _mockLogger.Object);
        }

        [Fact]
        public async Task ListarPedidos_DeveRetornarOkComLista()
        {
            // Arrange
            var pedidos = new List<PedidoResponseDto>
            {
                new PedidoResponseDto { Id = Guid.NewGuid(), ValorTotal = 100m },
                new PedidoResponseDto { Id = Guid.NewGuid(), ValorTotal = 200m }
            };
            
            _mockService
                .Setup(s => s.ListarPedidosAsync())
                .ReturnsAsync(pedidos);

            // Act
            var resultado = await _controller.ListarPedidos();

            // Assert
            var okResult = Assert.IsType<OkObjectResult>(resultado.Result);
            var retornoPedidos = Assert.IsAssignableFrom<IEnumerable<PedidoResponseDto>>(okResult.Value);
            Assert.Equal(2, retornoPedidos.Count());
        }

        [Fact]
        public async Task ObterPedido_PedidoExistente_DeveRetornarOk()
        {
            // Arrange
            var pedidoId = Guid.NewGuid();
            var pedido = new PedidoResponseDto { Id = pedidoId, ValorTotal = 150m };
            
            _mockService
                .Setup(s => s.ObterPedidoPorIdAsync(pedidoId))
                .ReturnsAsync(pedido);

            // Act
            var resultado = await _controller.ObterPedido(pedidoId);

            // Assert
            var okResult = Assert.IsType<OkObjectResult>(resultado.Result);
            var retornoPedido = Assert.IsType<PedidoResponseDto>(okResult.Value);
            Assert.Equal(pedidoId, retornoPedido.Id);
        }

        [Fact]
        public async Task ObterPedido_PedidoInexistente_DeveRetornarNotFound()
        {
            // Arrange
            var pedidoId = Guid.NewGuid();
            
            _mockService
                .Setup(s => s.ObterPedidoPorIdAsync(pedidoId))
                .ReturnsAsync((PedidoResponseDto)null);

            // Act
            var resultado = await _controller.ObterPedido(pedidoId);

            // Assert
            Assert.IsType<NotFoundObjectResult>(resultado.Result);
        }

        [Fact]
        public async Task CriarPedido_DadosValidos_DeveRetornarCreated()
        {
            // Arrange
            var dto = new CriarPedidoDto { ClienteId = Guid.NewGuid(), ValorTotal = 100m };
            var pedidoCriado = new PedidoResponseDto { Id = Guid.NewGuid(), ClienteId = dto.ClienteId, ValorTotal = dto.ValorTotal };
            
            _mockService
                .Setup(s => s.CriarPedidoAsync(dto))
                .ReturnsAsync(pedidoCriado);

            // Act
            var resultado = await _controller.CriarPedido(dto);

            // Assert
            var createdResult = Assert.IsType<CreatedAtActionResult>(resultado.Result);
            var retornoPedido = Assert.IsType<PedidoResponseDto>(createdResult.Value);
            Assert.Equal(pedidoCriado.Id, retornoPedido.Id);
        }
    }
}
```

### Teste de Repositório (Infrastructure)

**Localização**: `tests/[Projeto].Infrastructure.Tests/Repositories/`

```csharp
using Microsoft.EntityFrameworkCore;
using Xunit;
using [Projeto].Domain.Entities;
using [Projeto].Infrastructure.Data;
using [Projeto].Infrastructure.Data.Repositories;

namespace [Projeto].Infrastructure.Tests.Repositories
{
    public class RepositorioPedidosTests : IDisposable
    {
        private readonly AppDbContext _context;
        private readonly RepositorioPedidos _repositorio;

        public RepositorioPedidosTests()
        {
            var options = new DbContextOptionsBuilder<AppDbContext>()
                .UseInMemoryDatabase(databaseName: Guid.NewGuid().ToString())
                .Options;

            _context = new AppDbContext(options);
            _repositorio = new RepositorioPedidos(_context);
        }

        [Fact]
        public async Task AdicionarAsync_DeveAdicionarPedidoAoBanco()
        {
            // Arrange
            var pedido = new Pedido(Guid.NewGuid(), 100m);

            // Act
            var resultado = await _repositorio.AdicionarAsync(pedido);

            // Assert
            Assert.NotNull(resultado);
            Assert.Equal(1, await _context.Pedidos.CountAsync());
        }

        [Fact]
        public async Task ObterPorIdAsync_PedidoExistente_DeveRetornarPedido()
        {
            // Arrange
            var pedido = new Pedido(Guid.NewGuid(), 100m);
            await _context.Pedidos.AddAsync(pedido);
            await _context.SaveChangesAsync();

            // Act
            var resultado = await _repositorio.ObterPorIdAsync(pedido.Id);

            // Assert
            Assert.NotNull(resultado);
            Assert.Equal(pedido.Id, resultado.Id);
        }

        [Fact]
        public async Task ObterPorIdAsync_PedidoInexistente_DeveRetornarNull()
        {
            // Arrange
            var pedidoId = Guid.NewGuid();

            // Act
            var resultado = await _repositorio.ObterPorIdAsync(pedidoId);

            // Assert
            Assert.Null(resultado);
        }

        public void Dispose()
        {
            _context.Database.EnsureDeleted();
            _context.Dispose();
        }
    }
}
```

---

## 📝 Criação de Entidades

Para criar entidades, siga o [Guia de Entidades](./guia-entidades.md) e as [Nomenclatura de Propriedades em Entidades](./nomenclatura-propriedades-entidade.md).

### Checklist para Criação de Entidades

- [ ] Herdar de `BaseEntity` da biblioteca `ZDZCode.Data.EntityFramework`
- [ ] Utilizar PascalCase para propriedades públicas
- [ ] Adicionar `private set` para propriedades que não devem ser alteradas diretamente
- [ ] Criar construtor privado para Entity Framework
- [ ] Criar construtor público com validações de negócio
- [ ] Prefixar booleanos com `Is`, `Has` ou `Should`
- [ ] Usar nomes no plural para coleções: `Itens`, `Pedidos`
- [ ] Criar classe de configuração implementando `IEntityTypeConfiguration<T>`
- [ ] Registrar configuração no `DbContext` com `ApplyConfigurationsFromAssembly`

---

## 🎨 Implementação Frontend

### Estrutura de Projeto Frontend (Nuxt 3)

```
src/
├── components/           # Componentes Vue reutilizáveis
│   ├── common/
│   └── pedidos/
├── composables/         # Composables do Vue 3
├── layouts/             # Layouts da aplicação
├── pages/               # Páginas (roteamento automático)
│   └── pedidos/
├── plugins/             # Plugins do Nuxt
├── stores/              # Stores do Pinia
├── types/               # TypeScript types
└── utils/               # Funções utilitárias
```

### Passo 1: Criar Type Definition

**Localização**: `src/types/pedido.ts`

```typescript
export interface Pedido {
  id: string
  criadoEm: string
  atualizadoEm?: string
  status: string
  valorTotal: number
  clienteId: string
}

export interface CriarPedidoDto {
  clienteId: string
  valorTotal: number
  itens: ItemPedidoDto[]
}

export interface ItemPedidoDto {
  produtoId: string
  quantidade: number
  valorUnitario: number
}
```

### Passo 2: Criar Composable para API

**Localização**: `src/composables/usePedidos.ts`

```typescript
import type { Pedido, CriarPedidoDto } from '~/types/pedido'

export const usePedidos = () => {
  const config = useRuntimeConfig()
  const baseUrl = config.public.apiBase

  const listarPedidos = async (): Promise<Pedido[]> => {
    try {
      const { data, error } = await useFetch<Pedido[]>(`${baseUrl}/api/pedidos`)
      if (error.value) {
        throw new Error(`Erro ao listar pedidos: ${error.value.message}`)
      }
      return data.value || []
    } catch (error) {
      console.error('Erro ao listar pedidos:', error)
      throw error
    }
  }

  const obterPedido = async (id: string): Promise<Pedido | null> => {
    try {
      const { data, error } = await useFetch<Pedido>(`${baseUrl}/api/pedidos/${id}`)
      if (error.value) {
        throw new Error(`Erro ao obter pedido: ${error.value.message}`)
      }
      return data.value
    } catch (error) {
      console.error('Erro ao obter pedido:', error)
      throw error
    }
  }

  const criarPedido = async (dto: CriarPedidoDto): Promise<Pedido> => {
    try {
      const { data, error } = await useFetch<Pedido>(`${baseUrl}/api/pedidos`, {
        method: 'POST',
        body: dto
      })
      if (error.value) {
        throw new Error(`Erro ao criar pedido: ${error.value.message}`)
      }
      if (!data.value) {
        throw new Error('Resposta inválida do servidor')
      }
      return data.value
    } catch (error) {
      console.error('Erro ao criar pedido:', error)
      throw error
    }
  }

  return {
    listarPedidos,
    obterPedido,
    criarPedido
  }
}
```

### Passo 3: Criar Store Pinia

**Localização**: `src/stores/pedidos.ts`

```typescript
import { defineStore } from 'pinia'
import type { Pedido, CriarPedidoDto } from '~/types/pedido'

export const usePedidosStore = defineStore('pedidos', () => {
  const pedidos = ref<Pedido[]>([])
  const pedidoAtual = ref<Pedido | null>(null)
  const carregando = ref(false)
  const erro = ref<string | null>(null)

  const { listarPedidos, obterPedido, criarPedido } = usePedidos()

  const carregarPedidos = async () => {
    carregando.value = true
    erro.value = null
    try {
      pedidos.value = await listarPedidos()
    } catch (e) {
      erro.value = 'Erro ao carregar pedidos'
      console.error(e)
    } finally {
      carregando.value = false
    }
  }

  const carregarPedido = async (id: string) => {
    carregando.value = true
    erro.value = null
    try {
      pedidoAtual.value = await obterPedido(id)
    } catch (e) {
      erro.value = 'Erro ao carregar pedido'
      console.error(e)
    } finally {
      carregando.value = false
    }
  }

  const adicionarPedido = async (dto: CriarPedidoDto) => {
    carregando.value = true
    erro.value = null
    try {
      const novoPedido = await criarPedido(dto)
      pedidos.value.push(novoPedido)
      return novoPedido
    } catch (e) {
      erro.value = 'Erro ao criar pedido'
      console.error(e)
      throw e
    } finally {
      carregando.value = false
    }
  }

  return {
    pedidos,
    pedidoAtual,
    carregando,
    erro,
    carregarPedidos,
    carregarPedido,
    adicionarPedido
  }
})
```

### Passo 4: Criar Componente de Listagem

**Localização**: `src/components/pedidos/ListaPedidos.vue`

```vue
<template>
  <v-container>
    <v-row>
      <v-col>
        <h1 class="text-h4 mb-4">Pedidos</h1>
      </v-col>
    </v-row>

    <v-row v-if="carregando">
      <v-col class="text-center">
        <v-progress-circular indeterminate color="primary" />
      </v-col>
    </v-row>

    <v-row v-else-if="erro">
      <v-col>
        <v-alert type="error" closable>
          {{ erro }}
        </v-alert>
      </v-col>
    </v-row>

    <v-row v-else>
      <v-col>
        <v-data-table
          :headers="headers"
          :items="pedidos"
          :items-per-page="10"
          class="elevation-1"
        >
          <template #item.valorTotal="{ item }">
            {{ formatarMoeda(item.valorTotal) }}
          </template>

          <template #item.criadoEm="{ item }">
            {{ formatarData(item.criadoEm) }}
          </template>

          <template #item.actions="{ item }">
            <v-btn
              icon="mdi-eye"
              size="small"
              variant="text"
              @click="verDetalhes(item.id)"
            />
          </template>
        </v-data-table>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup lang="ts">
import { usePedidosStore } from '~/stores/pedidos'

const store = usePedidosStore()
const router = useRouter()

const { pedidos, carregando, erro } = storeToRefs(store)

const headers = [
  { title: 'ID', key: 'id', sortable: true },
  { title: 'Cliente', key: 'clienteId', sortable: true },
  { title: 'Valor Total', key: 'valorTotal', sortable: true },
  { title: 'Status', key: 'status', sortable: true },
  { title: 'Data Criação', key: 'criadoEm', sortable: true },
  { title: 'Ações', key: 'actions', sortable: false }
]

const formatarMoeda = (valor: number) => {
  return new Intl.NumberFormat('pt-BR', {
    style: 'currency',
    currency: 'BRL'
  }).format(valor)
}

const formatarData = (data: string) => {
  return new Date(data).toLocaleDateString('pt-BR')
}

const verDetalhes = (id: string) => {
  router.push(`/pedidos/${id}`)
}

// Carregar pedidos ao montar componente
onMounted(() => {
  store.carregarPedidos()
})
</script>
```

### Passo 5: Criar Página

**Localização**: `src/pages/pedidos/index.vue`

```vue
<template>
  <div>
    <ListaPedidos />
  </div>
</template>

<script setup lang="ts">
definePageMeta({
  layout: 'default',
  middleware: 'auth' // Se tiver autenticação
})
</script>
```

---

## 💻 Padrões de Código

### Backend

#### Nomenclatura de Variáveis (Seguir [nomenclatura-variaveis-servico.md](./nomenclatura-variaveis-servico.md))

- **camelCase**: variáveis locais e parâmetros
- **PascalCase**: propriedades públicas, classes, métodos
- **_camelCase**: campos privados com underscore
- Prefixar booleanos com `is`, `has`, `should`
- Usar nomes descritivos em português
- Evitar abreviações não padronizadas

```csharp
// ✅ Correto
private readonly IRepositorioPedidos _repositorioPedidos;
public async Task<Pedido> CriarPedidoAsync(PedidoDto pedidoDto)
{
    var novoPedido = new Pedido();
    var isValido = ValidarPedido(novoPedido);
}

// ❌ Incorreto
private IRepositorioPedidos repPed;
public async Task<Pedido> CriarPed(PedidoDto dto)
{
    var p = new Pedido();
    var v = ValidarPedido(p);
}
```

#### Programação Assíncrona

```csharp
// ✅ Use async/await para operações de I/O
public async Task<Pedido> ObterPedidoAsync(Guid id)
{
    return await _context.Pedidos
        .Include(p => p.Itens)
        .FirstOrDefaultAsync(p => p.Id == id);
}

// ❌ Evite bloqueios síncronos
public Pedido ObterPedido(Guid id)
{
    return _context.Pedidos
        .Include(p => p.Itens)
        .FirstOrDefault(p => p.Id == id);
}
```

#### Injeção de Dependência

```csharp
// ✅ Use interfaces e injeção de dependência
public class PedidoService
{
    private readonly IRepositorioPedidos _repositorio;
    
    public PedidoService(IRepositorioPedidos repositorio)
    {
        _repositorio = repositorio;
    }
}

// ❌ Evite criar instâncias diretamente
public class PedidoService
{
    private readonly RepositorioPedidos _repositorio = new RepositorioPedidos();
}
```

### Frontend

#### Composition API

```typescript
// ✅ Use ref() para primitivas e reactive() para objetos
const contador = ref(0)
const usuario = reactive({
  nome: '',
  email: ''
})

// ✅ Use computed para valores derivados
const nomeCompleto = computed(() => `${usuario.nome} ${usuario.sobrenome}`)

// ✅ Use watch para reações
watch(() => usuario.email, (novoEmail) => {
  console.log('Email alterado:', novoEmail)
})
```

#### Nomenclatura

- **camelCase**: variáveis, funções, propriedades
- **PascalCase**: componentes, interfaces, types
- Prefixar composables com `use`: `usePedidos`, `useAuth`
- Prefixar types/interfaces com letra maiúscula

---

## 🔄 Integração e CI/CD

### Estrutura de Pipeline Azure DevOps

**Arquivo**: `azure-pipelines.yml`

```yaml
trigger:
  branches:
    include:
      - main
      - develop

pool:
  vmImage: 'ubuntu-latest'

variables:
  buildConfiguration: 'Release'
  dotnetVersion: '8.0.x'

stages:
- stage: Build
  jobs:
  - job: BuildBackend
    steps:
    - task: UseDotNet@2
      inputs:
        version: $(dotnetVersion)
    
    - task: DotNetCoreCLI@2
      displayName: 'Restore'
      inputs:
        command: 'restore'
        projects: '**/*.csproj'
    
    - task: DotNetCoreCLI@2
      displayName: 'Build'
      inputs:
        command: 'build'
        projects: '**/*.csproj'
        arguments: '--configuration $(buildConfiguration)'
    
    - task: DotNetCoreCLI@2
      displayName: 'Run Tests'
      inputs:
        command: 'test'
        projects: '**/*Tests.csproj'
        arguments: '--configuration $(buildConfiguration) --collect:"XPlat Code Coverage"'
    
    - task: PublishCodeCoverageResults@1
      inputs:
        codeCoverageTool: 'cobertura'
        summaryFileLocation: '$(Agent.TempDirectory)/**/*coverage.cobertura.xml'

  - job: BuildFrontend
    steps:
    - task: NodeTool@0
      inputs:
        versionSpec: '20.x'
    
    - script: |
        yarn install
        yarn lint
        yarn build
      displayName: 'Install, Lint and Build Frontend'
      workingDirectory: 'src/frontend'

- stage: Deploy
  dependsOn: Build
  condition: and(succeeded(), eq(variables['Build.SourceBranch'], 'refs/heads/main'))
  jobs:
  - job: DeployToProduction
    steps:
    - task: AzureWebApp@1
      inputs:
        azureSubscription: 'YourAzureSubscription'
        appName: 'YourAppName'
        package: '$(System.DefaultWorkingDirectory)/**/*.zip'
```

### Commits (Seguir [guia-de-commits.md](./guia-de-commits.md))

Formato: `<tipo>: <descrição curta>`

Tipos:
- `feat`: nova funcionalidade
- `fix`: correção de bug
- `docs`: alterações na documentação
- `test`: adição ou modificação de testes
- `refactor`: refatoração de código
- `chore`: tarefas de manutenção

Exemplos:
```
feat: adicionar endpoint de criação de pedidos
fix: corrigir validação de CPF no cadastro
test: adicionar testes unitários para PedidoService
docs: atualizar documentação da API
```

---

## ✅ Checklist Completo de Implementação

### Endpoint API
- [ ] Criar entidade no Domain herdando de BaseEntity
- [ ] Criar enum se necessário
- [ ] Criar configuração do EF Core
- [ ] Criar DTOs na Application
- [ ] Criar interface do repositório no Domain
- [ ] Implementar repositório na Infrastructure
- [ ] Criar interface e implementação do serviço na Application
- [ ] Criar controller na API
- [ ] Adicionar documentação XML nos métodos públicos
- [ ] Registrar dependências no DI container
- [ ] Criar testes unitários para entidade
- [ ] Criar testes unitários para serviço
- [ ] Criar testes unitários para controller
- [ ] Criar testes de integração para repositório
- [ ] Documentar endpoint no Swagger

### Feature Frontend
- [ ] Criar type definitions
- [ ] Criar composable para API
- [ ] Criar store Pinia
- [ ] Criar componentes reutilizáveis
- [ ] Criar páginas
- [ ] Adicionar validações nos formulários
- [ ] Implementar tratamento de erros
- [ ] Adicionar loading states
- [ ] Criar testes unitários para composables
- [ ] Criar testes unitários para stores
- [ ] Criar testes de componente

### Pipeline CI/CD
- [ ] Configurar build no Azure DevOps
- [ ] Adicionar testes automatizados no pipeline
- [ ] Configurar code coverage
- [ ] Configurar deploy automático
- [ ] Adicionar validação de lint
- [ ] Configurar ambientes (dev, staging, production)

---

## 📚 Referências

- [Padrões e Diretrizes](./padroes-e-diretrizes.md)
- [Guia de Commits](./guia-de-commits.md)
- [Guia de Entidades](./guia-entidades.md)
- [Nomenclatura de Variáveis em Serviços](./nomenclatura-variaveis-servico.md)
- [Nomenclatura de Propriedades em Entidades](./nomenclatura-propriedades-entidade.md)
- [ASP.NET Core Best Practices](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/best-practices)
- [Vue 3 Composition API](https://vuejs.org/guide/extras/composition-api-faq.html)
- [Nuxt 3 Documentation](https://nuxt.com/)
- [Pinia Documentation](https://pinia.vuejs.org/)

---

Este guia deve ser utilizado como referência principal ao implementar novas funcionalidades no ecossistema ZDZCode. Mantenha-o atualizado conforme novas práticas forem adotadas.
