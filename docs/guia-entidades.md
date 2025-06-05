# Guia de Entidades

Este documento explica como criar classes de entidade seguindo as práticas da comunidade **ZDZCode** e mantendo a compatibilidade com a biblioteca `ZDZCode.Data.EntityFramework`.

## Localização das Entidades

- Armazene as classes de entidade no projeto de domínio (por exemplo `src/Domain`).
- Organize-as em pastas por contexto ou agregados, mantendo nomes descritivos.

## Herança Básica

A biblioteca `ZDZCode.Data.EntityFramework` provê uma classe base comum chamada `BaseEntity`. Utilize-a como classe pai de todas as entidades para garantir integração com o Entity Framework e os utilitários da ZDZCode.

```csharp
public abstract class BaseEntity
{
    public Guid Id { get; protected set; }
}
```

Uma nova entidade deve herdar de `BaseEntity`:

```csharp
public class Produto : BaseEntity
{
    // propriedades da entidade
}
```

## Propriedades Obrigatórias

1. **Id**: chave primária do tipo `Guid` ou `int`, definida na classe base.
2. **Datas de criação e atualização** (opcional): utilize `DateTime` com as convenções `CreatedAt` e `UpdatedAt` se o domínio exigir auditoria.

## Nomenclatura de Propriedades

Para convenções detalhadas, consulte [Nomenclatura de Propriedades em Entidades](./nomenclatura-propriedades-entidade.md).

- Use **PascalCase** em todas as propriedades públicas.
- Utilize nomes no singular que representem o conceito de negócio.
- Prefira nomes claros em português, evitando abreviações desnecessárias.
- Para campos booleanos, prefixe com `Is`, `Has` ou `Should`.
- Propriedades de navegação para coleções devem estar no plural: `Itens`, `Pedidos`.

Exemplo:

```csharp
public class Pedido : BaseEntity
{
    public DateTime CriadoEm { get; private set; }
    public StatusPedido Status { get; private set; }
    public ICollection<ItemPedido> Itens { get; private set; }
}
```

## Mapeamento com Entity Framework

Defina o mapeamento das entidades em classes de configuração implementando `IEntityTypeConfiguration<T>`. Isso mantém o código de domínio limpo e separado das configurações de banco.

```csharp
public class PedidoConfiguration : IEntityTypeConfiguration<Pedido>
{
    public void Configure(EntityTypeBuilder<Pedido> builder)
    {
        builder.ToTable("Pedidos");
        builder.HasKey(p => p.Id);
        builder.Property(p => p.CriadoEm).IsRequired();
    }
}
```

Registre as configurações no `DbContext` do projeto que consome `ZDZCode.Data.EntityFramework`.

## Boas Práticas

- Evite colocar lógica de negócio complexa dentro das entidades; utilize serviços ou agregados.
- Mantenha as entidades focadas em representar o estado do domínio.
- Use propriedades `private set` ou métodos para controlar alterações quando necessário.

Seguindo estas recomendações suas entidades continuarão compatíveis com a biblioteca de acesso a dados da ZDZCode e padronizadas com o restante dos projetos.

