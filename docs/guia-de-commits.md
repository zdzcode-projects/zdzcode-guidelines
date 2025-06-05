# Guia de Commits e Branches

Este documento define as convenções de commits e branches utilizadas nos projetos ZDZCode.

## Mensagens de Commit

- Utilize frases curtas e objetivas no imperativo.
- Evite mensagens genéricas como "update" ou "fix".
- Quando pertinente, referencie o código da issue.

Exemplo:

```
feat: adicionar validacao de CPF
```

## Estratégia de Branches

1. `main` deve refletir o código estável e pronto para produção.
2. `develop` (quando existente) agrega funcionalidades que serão lançadas.
3. Crie branches descritivas para cada tarefa:
   - `feature/nome-breve`
   - `bugfix/nome-breve`

## Tags e Releases

- Utilize [Semantic Versioning](https://semver.org/lang/pt-BR/) para numerar releases.
- Marque versões estáveis com tags correspondentes.

