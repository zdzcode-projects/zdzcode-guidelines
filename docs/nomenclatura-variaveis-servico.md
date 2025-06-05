# Nomenclatura de Variáveis em Classes de Serviço

Este guia define padrões para nomear variáveis dentro de classes de serviço nos projetos **ZDZCode**. O objetivo é manter consistência e facilitar a compreensão do código por toda a equipe.

## Regras Gerais

1. Utilize **camelCase** para variáveis locais e parâmetros.
2. Use **PascalCase** apenas para propriedades públicas.
3. Evite abreviações que não sejam amplamente conhecidas.
4. Prefira nomes descritivos que expressem claramente o propósito da variável.

## Exemplos

```csharp
public class PedidoService
{
    private readonly IRepositorioPedidos _repositorioPedidos; // Campo privado

    public PedidoService(IRepositorioPedidos repositorioPedidos)
    {
        _repositorioPedidos = repositorioPedidos;
    }

    public async Task<Pedido> CriarPedidoAsync(PedidoDto pedidoDto)
    {
        // Variáveis locais em camelCase
        var novoPedido = new Pedido();
        var dataCriacao = DateTime.UtcNow;
        // ...
        return await _repositorioPedidos.AdicionarAsync(novoPedido);
    }
}
```

## Boas Práticas

- Prefixe variáveis booleanas com verbos como `is`, `has` ou `should`.
- Para coleções, utilize nomes no plural: `pedidos`, `itens`.
- Mantenha siglas com a primeira letra maiúscula e o restante em minúsculas, por exemplo: `idPedido`.

Seguir estas convenções ajuda a evitar ambiguidades e melhora a legibilidade das classes de serviço.
