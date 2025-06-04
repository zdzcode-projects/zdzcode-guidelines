# Padrões e Diretrizes

Este documento descreve boas práticas e padrões recomendados para projetos mantidos pela comunidade ZDZCode.

## Estrutura de Repositórios

1. Utilize um `README.md` com objetivo do projeto e instruções básicas.
2. Adote uma convenção de nomes consistente para branches e commits.

## Estilo de Código

- Prefira código limpo e autocomentado.
- Utilize ferramentas de formatação e linters adequadas à linguagem do projeto.

## Pull Requests

1. Descreva de forma objetiva o que está sendo alterado.
2. Inclua referência a issues quando aplicável.
3. Mantenha o escopo do PR focado em uma única tarefa.

## Estrutura de Branches

1. Utilize `main` como branch principal.
2. Crie branches de funcionalidades usando `feature/nome-curto`.
3. Para correções utilize `fix/descricao`.
4. Hotfixes devem seguir `hotfix/descricao`.

## Mensagens de Commit

- Escreva no imperativo descrevendo o que o commit faz.
- Limite a linha de resumo a 72 caracteres.
- Quando necessário adicione um corpo após uma linha em branco.

## Versionamento Semântico

- Utilize o formato `MAJOR.MINOR.PATCH`.
- `MAJOR` para mudanças incompatíveis.
- `MINOR` para novas funcionalidades compatíveis.
- `PATCH` para correções de bugs.

## Revisões de Código

- Todo PR deve ser revisado por pelo menos uma pessoa.
- Use os comentários da revisão para discutir melhorias.

## Licença

Os documentos seguem a licença MIT, presente neste repositório.
