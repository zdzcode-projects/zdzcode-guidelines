# Nomenclatura de Propriedades em Entidades

Este guia detalha como nomear propriedades em classes de entidade para manter a consistência dos projetos **ZDZCode** e garantir total compatibilidade com a biblioteca `ZDZCode.Data.EntityFramework`.

Consulte o [Guia de Entidades](./guia-entidades.md) para um passo a passo completo de criação e mapeamento.

## Regras Básicas

1. Utilize **PascalCase** em todas as propriedades públicas.
2. Prefira nomes no singular que representem claramente o conceito de negócio.
3. Evite abreviações que não sejam amplamente conhecidas.
4. Prefixe campos booleanos com `Is`, `Has` ou `Should` para indicar que o valor é booleano.
5. Propriedades de navegação para coleções devem estar no plural.
6. Ao utilizar enums, nomeie a propriedade apenas com o significado do domínio e mantenha o tipo do enum separado.

```csharp
public class Produto : BaseEntity
{
    public string Nome { get; private set; }
    public decimal Preco { get; private set; }
    public bool IsAtivo { get; private set; }
    public Categoria Categoria { get; private set; }
    public ICollection<ItemPedido> ItensPedido { get; private set; }
}
```

Seguir estas diretrizes melhora a legibilidade do código e mantém o funcionamento com `ZDZCode.Data.EntityFramework`.
